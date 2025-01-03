---
layout: post
title: "Inside InnoDB: A Straightforward Look at Undo Logging and History"
date: 2024-12-30 19:00:00 +0000
categories: mysql innodb mvcc
---

Imagine you have a little library where people come in and update books all day long. Some mark pages with notes, some correct typos, and some even rip out pages. Yet, no matter what, each visitor wants to see a neat, coherent set of books—no partial changes, no missing pages, no confusion. How do we keep everyone happy and ensure all those changes don’t collide?

In MySQL’s InnoDB storage engine, this puzzle is solved by something called multi-version concurrency control (`MVCC`). It sounds fancy, but the idea boils down to giving different visitors the exact “version” of the library they’re allowed to see, even if changes are happening at the same time. Let’s see how this works!

## 1. So, What is MVCC?
MVCC stands for `multi-version concurrency control`. It’s a method that stores multiple snapshots, or versions, of data so many people (transactions) can read or write without constantly stepping on each other’s toes. Instead of halting everyone with big locks, InnoDB quietly keeps older versions of data around for readers that need them.

When a transaction (think of it as your user session) starts reading or writing, InnoDB says, “Hey, we’ll keep track of the changes in a special place. You, dear transaction, continue to see data exactly as it was at a specific point in time.” By doing this, readers don’t block writers, and writers don’t block readers. Everyone gets their own consistent view.

## 2. The Undo Logs: Saving Old Data
Now, here’s the fun part: how do we hold on to older versions when new versions are being written? 

**Enter the undo log.**

Undo logs are like time machines. Whenever InnoDB changes a row (say, in your library, a page in a book), it doesn’t discard the old version. It copies that old version into the undo log first, then applies the new change to the main data.
Each row in the database points to its latest “undo record.” That record knows about the version before it, and that previous version knows about the one before it, and so on. Following that chain lets you hop backward in time to see earlier states of the row.
So, if you picture your library’s “main collection” as the brand-new data, the undo logs are the dusty storeroom where we kept the old pages. If you need them (for a transaction that started long ago), they’re still there to be retrieved.

## 3. Transaction Isolation Levels: The Ground Rules
In MySQL/InnoDB, you’ve got a few ways to decide which version of the library you see. These are transaction isolation levels:

### READ UNCOMMITTED
Everything is as up-to-date as physically possible—even changes that haven’t been committed (finalized) yet. It’s like reading over someone’s shoulder while they’re still writing. 
### READ COMMITTED
Every time you issue a query, InnoDB says, “Which changes are committed at this moment? That’s what you get.” So between queries, you might see new data appear if it got committed.
### REPEATABLE READ
The default. When your transaction starts, you get a “snapshot” of the data as of that starting moment, and you keep seeing that same version for every query in your transaction—unless you make your own changes. Think of it as being handed a static copy of the library as of the moment you walked in.
### SERIALIZABLE
This one basically tacks on extra locks to prevent certain anomalies, but from a visibility perspective, it’s similar to REPEATABLE READ.

## 4. Data Pages Are Updated Immediately, Visibility is Controlled by Read Views

Here’s something surprising: the moment you update a row in InnoDB—even before you hit “commit” - the main copy of that row in the data pages is actually changed. This might sound risky, because it seems like other transactions could see your half-done work. But InnoDB has a clever trick called a read view to keep uncommitted changes hidden from others.

A read view is created when a transaction starts (for `Repeatable Read`) or at the beginning of each statement (for `Read Committed`). This read view contains a list of active transactions at that point in time. When a transaction attempts to read a row, InnoDB checks the row's transaction ID (the ID of the transaction that last modified the row) against the current transaction's read view.

- If the row was modified by a transaction that is still active (its ID is present in the read view), InnoDB retrieves the older version of the row from the undo log.
- If the row was modified by a transaction that has already committed (its ID is not present in the read view), the transaction reads the latest version directly from the data page.

This approach avoids the need to "walk back" through long chains of undo logs for every read. The read view efficiently determines the correct row version to use, ensuring that each transaction sees a consistent snapshot of the data according to its isolation level.


## 5. The Pitfalls of Long-Running Transactions
Now suppose a single visitor in your library sits down and starts reading. Then, half the day goes by while they’re still in the library, doing research under a transaction that started hours ago. Meanwhile, the library is buzzing with folks updating books. That old transaction has a “very old snapshot,” so every time it reads a row, InnoDB has to jump back through potentially layers and layers of undo records. That’s slow.

**But it gets worse**: InnoDB has a housekeeping job called purge that cleans up old versions it no longer needs. It can’t throw away versions if there’s still a transaction that might need them, so these old versions pile up. This can cause:

- Excessive Undo Logs: The undone pages grow like a stack of old newspapers you haven’t thrown out.
- Sluggish Performance: That ancient transaction has to keep scanning backward through multiple changes to find what it’s allowed to see.

That’s why you often hear, **“Long-running transactions are bad for MySQL.”** They clog up the system, forcing it to maintain a bunch of older versions for one slowpoke.

## 6. Delete Doesn’t Mean Gone
In the library, if you “delete” a book, you might expect it to vanish instantly from the shelf. But in InnoDB, a delete merely marks a row with a flag. Why? Because there could be a transaction (maybe that old guy in the corner) that still wants to read the older version. If the row disappeared completely, InnoDB wouldn’t be able to reconstruct the old view it promised that transaction. So it’s a “soft delete,” and only after the purge process sees it’s truly no longer needed does the row get physically removed.

## 7. The Purge Process and Undo Log Management

Think of the purge process as a diligent librarian who strolls around the shelves, quietly discarding notes that no one needs anymore. In InnoDB, every time you change data, the old row versions end up in undo logs—the notes tucked away for anyone who might still want to see the “past” state.

**How the Librarian Decides What to Toss**

- There’s a concept called the oldest active read view—basically, the earliest moment in time that any current transaction needs to reference.
- Anything older than that moment becomes fair game for the purge process to remove. After all, no one left in the library is interested in those older notes anymore.

**About Those ‘Deleted’ Rows**

- When you “delete” a row, you’re just flagging it as gone. It’s like slipping a note on the bookshelf that says, “This volume is outdated.”
- The purge process later comes along, sees that this row is both flagged and older than what any transaction needs, and permanently removes it—along with its undo log records.

**Tracking the Cleanup**

- If you check SHOW ENGINE INNODB STATUS, you’ll spot a metric called the history list length—it shows how many old notes (undo records) are still lying around, waiting for the librarian (the purge process) to clean them up.
- If that list keeps growing and never seems to shrink, there’s probably a very old transaction that just won’t let go of the past, holding onto old row versions the purge process can’t throw away yet.


## 8. Under the Hood of Undo Logs

If you look even deeper, you’ll discover that InnoDB can use multiple undo tablespaces—basically, different warehouses to store older versions.

There’s also a difference between undo logs for freshly inserted rows (which can be undone by simply deleting the inserted row) and those for updated rows (which keep track of what the old values were). Undo log records are created for inserts, though they are simpler and allow for efficient rollback by directly removing the inserted row. For most people, these internal structures matter less than understanding the big picture: each row can be reconstructed if you follow the chain of undo records. But if you’re a tuning enthusiast, you might adjust how many undo tablespaces you have and how they’re organized.

## 9. Tips and Tricks

**Keep Transactions Short**
The shorter your transaction, the fewer old row versions build up—and the faster purge can do its job.

**Pick the Right Isolation Level**
Don’t choose `REPEATABLE READ` if you don’t need a consistent view for your entire transaction. `READ COMMITTED` or even `READ UNCOMMITTED` (☉_☉) can lighten the burden on the system. But be very cautious about going all the way down to `READ UNCOMMITTED`. That’s like peeking behind the curtain while stagehands rearrange the set—you might see props that never make it into the final show. Sure, it’s faster, but you’re risking `phantom` data that could vanish before you’re done using it. So only pick `READ UNCOMMITTED` if you truly know what you’re doing and can live with seeing half-baked changes.

**Watch Your History List**
A growing list means the system is storing more and more old changes. Often this is a symptom of an uncommitted or “stuck” transaction.

**Don’t Let Transactions Go Zombie**
Sometimes a script fails, and you’ve got an open transaction lingering in your application. That stuck transaction can freeze your purge process.

If MySQL detects that a transaction remains open (i.e., not committed or rolled back), it won’t automatically close or roll it back unless the client connection is lost or explicitly killed. As a result, the so-called “zombie” transaction can linger, preventing InnoDB’s purge process from doing its job. Here’s how you can address the issue: Use `SHOW PROCESSLIST;` for active transactions and their ages.

**Tune for Write-Heavy Workloads**
If you’re doing a ton of writes, consider adjusting parameters like `innodb_purge_threads` to help the purge keep up with the pace of incoming changes.

InnoDB’s MVCC is a brilliant way to let lots of people read and write data simultaneously without tripping over each other. It’s like letting each library visitor see their own version of the shelves, complete with changes they’re allowed to see. The magic tool that makes this happen is the undo log, which preserves old versions whenever changes occur.

But, all that magic can get costly if you let transactions linger for ages. Those old versions add up, and InnoDB can’t clean them out until it’s sure nobody needs them anymore. Keep transactions tidy, choose the right isolation levels, and watch the history list to ensure you don’t end up with a warehouse of old books that nobody’s ever going to read again.

