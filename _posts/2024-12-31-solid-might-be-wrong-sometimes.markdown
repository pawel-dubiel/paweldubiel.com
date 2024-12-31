---
layout: post
title: "From Pizza to Principles: How Simplicity Beats Sacred Software Laws"
date: 2024-12-31 12:00:00 +0000
categories: code-design solid
---

I want to tell you a little tale about how software engineers like to turn guidelines into sacred laws—and sometimes lose sight of a simpler path. 

Imagine you’re making a pizza. You’ve got dough, sauce, cheese, and toppings—all simple, distinct parts. The dough isn’t trying to be the sauce, and the cheese doesn’t contain every topping under the sun; each ingredient has its own job. But when you layer them, you get a delicious meal. That’s how good code works: many small, focused components that, when combined, create something far richer than any single, monolithic “mega-ingredient.” If you keep each piece simple, you can easily swap out a topping or tweak the recipe—no grand pizza theory required.

In software, we have these `“SOLID”` principles—Single Responsibility, Open/Closed, Liskov Substitution, Interface Segregation, and Dependency Inversion. They sound grand, but let’s see if we can poke at them with a bit of curiosity.

## Single Responsibility Principle: The Phantom “One Reason to Change”
This principle says, “A class should have only one reason to change.”

When I first heard that, it sounded profound—like a philosophical statement! Then I asked, “Wait, what do we mean by ‘responsibility’? And who decides how big or small that ‘one reason’ can be?”

Imagine you’re cooking a meal. If you’re following the Single Responsibility Principle in the kitchen, you might say:

- The cutting board is for slicing veggies,
- The stove is for heating,
- The pan is for frying,
- The timer is fo... well, timing,

And so forth.

Sounds great. But if you watch a real cook, sometimes they’ll cut veggies in the pan directly, or they’ll warm up tortillas on the same stove burner they’re using for soup. If you tried to fit that “one reason to change” exactly into each tool, you might never finish dinner because you’d be so worried about going beyond each gadget’s “single responsibility.”

It’s good to keep things tidy, but guess what? Real tasks like building an ETL (Extract, Transform, Load) system can’t always be subdivided cleanly into neat compartments. It’s often enough to just keep your code small and easy to think about. If you can keep it in your head, you’re on track.

## Open/Closed Principle: The Add-On Tangle

Next is the Open/Closed Principle: “Software entities should be open for extension but closed for modification.” It’s like saying, “I have a children’s toy that you can attach add-ons to, but you can’t take anything out or alter the original design.” That sounds good at first—maybe you can keep stacking on new features. But eventually, your toy becomes a Frankenstein monster of plastic add-ons, weird levers, and extra wheels you can’t even remember how to use.

In real life, you’ll often get a new requirement that basically says, “Change the fundamental behavior of your code.” If you keep refusing to modify the old stuff, you’ll just keep piling on new layers, and soon your design is so tangled you’re afraid to touch anything at all.

A simpler approach? Write your code so it’s easy to change. If you need to rewrite or refactor some parts, go ahead. Don’t let the fear of modifying “old code” lead you to load your system with extra bells and whistles that only cause confusion.

## Liskov Substitution Principle: The Inheritance Rabbit Hole

Then there’s Liskov Substitution, which says that if you have a type, any of its subtypes should behave in the same way. Formally, it’s a beautiful idea—like saying if you can drive a car, you can drive any car. But think about cars: a heavy-duty truck and a compact sports car are both “cars,” yet if you get in a truck expecting sports car maneuvers, you might be in for a bumpy surprise.

In programming, it becomes an endless debate about whether a new class “is-a” this or “is-a” that—like kids arguing if a square is a rectangle or if a rectangle is a square. Mathematically, sure, the definitions line up. But does it actually help you cook dinner or get your code done on time?

A better question is, “Does this object do what I need?” That’s composition thinking. Instead of twisting yourself into a pretzel to prove “this is definitely a subtype of that,” just ask if the behaviors line up with the tasks at hand. You’ll avoid a lot of headaches.

## Interface Segregation Principle: Don’t Build a Swiss Army Chainsaw

The Interface Segregation Principle basically says that you shouldn’t mash every function under the sun into one giant interface. Yes, that’s obviously a good idea—like not building a single kitchen utensil that slices, peels, mashes, and stirs all in one monstrous apparatus the size of your entire sink. Of course, we prefer simpler tools.

What’s the big revelation here? Not much. The critique is that we already know it’s a bad idea to create an all-in-one “super-interface.” If your code starts to feel as heavy and cumbersome as a Swiss Army chainsaw, maybe it’s time to break it into smaller, more manageable parts.

## Dependency Inversion Principle: Let’s Not Worship Frameworks

Finally, there’s Dependency Inversion: “High-level modules shouldn’t depend on low-level modules; both should depend on abstractions.” Another fancy statement. I like to think of it as building a house: the architect (high-level) shouldn’t depend on the carpenter’s hammer (low-level). Instead, they should agree on “hammering nails” as an abstract concept.

But here’s the rub: sometimes you get roped into huge dependency-injection frameworks just to keep everything “decoupled,” and you end up with complicated libraries that are harder to manage than the original, straightforward approach. If all you really needed was a hammer, there’s no need to write a 20-page contract specifying how the hammer interface should behave in all cosmic scenarios. You’re building a deck, not a space station.

Instead of worshipping abstraction, focus on tiny, composable pieces. If you can snap them together like LEGO blocks, you’re in good shape. Reusability will come naturally if the pieces are small, straightforward, and do what they say on the box.

## The Simplest Code Wins

When we look at all these principles—Single Responsibility, Open/Closed, Liskov Substitution, Interface Segregation, Dependency Inversion—a pattern emerges: each principle warns against complexity in a different way. Ironically, treating them as unbreakable commandments can make your code more complex.

The real message is simpler and more direct: Write code that you can easily hold in your head, test quickly, and change when you need to. It’s not about ignoring the principles; it’s about not letting them become dogma. Let them be friendly reminders instead of laws chiseled in stone.

To borrow from Einstein, a man who knew a thing or two about making complicated ideas elegantly simple: “Everything should be made as simple as possible, but not simpler.” If you keep that spirit in mind, your code will be clear, adaptable, and a pleasure to work with—and you might find that good design emerges almost naturally.

And that is the whole reason we build software in the first place: to make it work without driving ourselves crazy. Don’t get lost in sweeping theories or endless checklists. Remember how ants tackle giant tasks by doing small, simple things - and still manage to build entire colonies. If you stick to clarity, simplicity, and a dash of common sense, you’ll be amazed at how smoothly everything comes together.






