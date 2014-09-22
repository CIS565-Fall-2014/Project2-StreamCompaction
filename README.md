Project-2
=========

A Study in Parallel Algorithms : Stream Compaction

# INTRODUCTION
Many of the algorithms you have learned thus far in your career have typically
been developed from a serial standpoint.  When it comes to GPUs, we are mainly
looking at massively parallel work.  Thus, it is necessary to reorient our
thinking.  In this project, we will be implementing a couple different versions
of prefix sum.  We will start with a simple single thread serial CPU version,
and then move to a naive GPU version.  Each part of this homework is meant to
follow the logic of the previous parts, so please do not do this homework out of
order.

This project will serve as a stream compaction library that you may use (and
will want to use) in your
future projects.  For that reason, we suggest you create proper header and CUDA
files so that you can reuse this code later.  You may want to create a separate
cpp file that contains your main function so that you can test the code you
write.

# OVERVIEW
Stream compaction is broken down into two parts: (1) scan, and (2) scatter.

## SCAN
Scan or prefix sum is the summation of the elements in an array such that the
resulting array is the summation of the terms before it.  Prefix sum can either
be inclusive, meaning the current term is a summation of all the elements before
it and itself, or exclusive, meaning the current term is a summation of all
elements before it excluding itself. 

Inclusive:

In : [ 3 4 6 7 9 10 ]

Out : [ 3 7 13 20 29 39 ]

Exclusive

In : [ 3 4 6 7 9 10 ]

Out : [ 0 3 7 13 20 29 ]

Note that the resulting prefix sum will always be n + 1 elements if the input
array is of length n.  Similarly, the first element of the exclusive prefix sum
will always be 0.  In the following sections, all references to prefix sum will
be to the exclusive version of prefix sum.

## SCATTER
The scatter section of stream compaction takes the results of the previous scan
in order to reorder the elements to form a compact array.

For example, let's say we have the following array:
[ 0 0 3 4 0 6 6 7 0 1 ]

We would only like to consider the non-zero elements in this zero, so we would
like to compact it into the following array:
[ 3 4 6 6 7 1 ]

We can perform a transform on input array to transform it into a boolean array:

In :  [ 0 0 3 4 0 6 6 7 0 1 ]

Out : [ 0 0 1 1 0 1 1 1 0 1 ]

Performing a scan on the output, we get the following array :

In :  [ 0 0 1 1 0 1 1 1 0 1 ]

Out : [ 0 0 0 1 2 2 3 4 5 5 ]

Notice that the output array produces a corresponding index array that we can
use to create the resulting array for stream compaction. 

# PART 1 : REVIEW OF PREFIX SUM
Given the definition of exclusive prefix sum, please write a serial CPU version
of prefix sum.  You may write this in the cpp file to separate this from the
CUDA code you will be writing in your .cu file. 

# PART 2 : NAIVE PREFIX SUM
We will now parallelize this the previous section's code.  Recall from lecture
that we can parallelize this using a series of kernel calls.  In this portion,
you are NOT allowed to use shared memory.

### Questions 
* Compare this version to the serial version of exclusive prefix scan. Please
  include a table of how the runtimes compare on different lengths of arrays.
* Plot a graph of the comparison and write a short explanation of the phenomenon you
  see here.

# PART 3 : OPTIMIZING PREFIX SUM
In the previous section we did not take into account shared memory.  In the
previous section, we kept everything in global memory, which is much slower than
shared memory.

## PART 3a : Write prefix sum for a single block
Shared memory is accessible to threads of a block. Please write a version of
prefix sum that works on a single block.  

## PART 3b : Generalizing to arrays of any length.
Taking the previous portion, please write a version that generalizes prefix sum
to arbitrary length arrays, this includes arrays that will not fit on one block.

### Questions
* Compare this version to the parallel prefix sum using global memory.
* Plot a graph of the comparison and write a short explanation of the phenomenon
  you see here.

# PART 4 : ADDING SCATTER
First create a serial version of scatter by expanding the serial version of
prefix sum.  Then create a GPU version of scatter.  Combine the function call
such that, given an array, you can call stream compact and it will compact the
array for you.  Finally, write a version using thrust. 

### Questions
* Compare your version of stream compact to your version using thrust.  How do
  they compare?  How might you optimize yours more, or how might thrust's stream
  compact be optimized.

# EXTRA CREDIT (+10)
For extra credit, please optimize your prefix sum for work parallelism and to
deal with bank conflicts.  Information on this can be found in the GPU Gems
chapter listed in the references.  

# SUBMISSION
Please answer all the questions in each of the subsections above and write your
answers in the README by overwriting the README file.  In future projects, we
expect your analysis to be similar to the one we have led you through in this
project.  Like other projects, please open a pull request and email Harmony.

# REFERENCES
"Parallel Prefix Sum (Scan) with CUDA." GPU Gems 3.
