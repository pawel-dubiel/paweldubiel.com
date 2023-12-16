---
layout: post
title: "Note on Advancements in machine learning for machine learning"
date: 2023-12-16 10:00:00 +0000
categories: machine learning
---

[Source](https://blog.research.google/2023/12/advancements-in-machine-learning-for.html)

# Summary of Google DeepMind and Google Research Blog Post (December 15, 2023)

## Introduction
This blog post from Google DeepMind and Google Research focuses on the use of machine learning (ML) to enhance the efficiency of ML workloads. It particularly emphasizes advancements in ML compilers and the introduction of the TpuGraphs dataset.

## ML Compilers
- **Purpose**: Convert user-written ML programs into executable instructions.
- **Optimization Levels**:
  - Graph-level: Optimizes considering the entire computation graph.
  - Kernel-level: Focuses on optimizing individual graph kernels.
- **Example**: Optimization of memory layouts for tensor operations, enhancing overall computational efficiency.

## TpuGraphs Dataset
- **Objective**: To improve ML model efficiency through better compiler optimizations.
- **Content**:
  - Computational graphs from various ML workloads.
  - Compilation configurations and respective execution times.
- **Focus**: Layout and tiling configurations for Google's Tensor Processing Units (TPUs).
- **Scale**: Significantly larger than previous datasets, enabling more extensive research in graph-level prediction for large graphs.

## Baseline Learned Cost Models
- **Methodology**: Utilize Graph Neural Networks (GNNs) to represent programs as graphs.
- **Features**:
  - Node features encompass operation type and other relevant characteristics.
- **Process**: Employs graph pooling to derive a fixed-size graph embedding, used for predicting output.

## Graph Segment Training (GST)
- **Aim**: To scale GNN training for handling large graphs on devices with limited memory capacity.
- **Technique**:
  - Partitioning large graphs into smaller segments.
  - Model updates using a subset of segments while combining embeddings for the complete graph.
