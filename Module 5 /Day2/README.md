# Day 02 • Loops in Verilog








## Introduction to Loops

In Verilog, loops are used to repeat a set of statements efficiently instead of writing the same code multiple times. They are especially useful when designing hardware structures that contain repeated logic or when processing multiple bits of a signal.

### for Loop

A for loop repeats a block of procedural statements a specific number of times. It is commonly used inside always blocks for repetitive operations such as bit-by-bit processing.

### Generate Loop

A generate loop is used to create multiple hardware instances or repeated structures during elaboration. Unlike a procedural for loop, a generate loop describes hardware that is replicated in the final design.

In short:
for loop → repeats procedural operations
generate loop → replicates hardware structures
