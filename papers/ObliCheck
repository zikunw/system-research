ObliCheck: Efficient Verification of Oblivious Algorithms with Unobservable State

Check for a subset of javascript.

Allow checking for unobservable memory space to attacker.
- Previous methods does not allow to do that.

Two novel ideas:
1. **Optimistic state merging**: merging two unobservable values by introducing a new unconstrained symbolic value for over-approximating the two unobservable values.
2. **Iterative state unmerging**: address the issue that the previous method might trigger an execution path which would have been impossible before merging. It records symbolic variables merged during the execution. It iteratively refines its judgment by backtracking the execution and unmerges a part of merged variables which may have caused the wrong prognosis.

The checker also automatically **generate a loop invariant for common loop patterns** employed in oblivious algorithms: each iteration of a loop appends the same constant number of elements to the output buffer. 

The checking time grows linearly as the number of the input records grows.
