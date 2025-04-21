1. The pseudocode below illustrates the basic push() and pop() operations of an array-based stack. Assuming that this algorithm is used in a
concurrent environment, answer the following questions:
(a) Which data have a race condition?
(b) How could the race condition be fixed?


> a, top
> b, Use mutex lock