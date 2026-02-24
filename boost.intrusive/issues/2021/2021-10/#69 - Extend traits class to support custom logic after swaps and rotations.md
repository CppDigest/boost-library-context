# #69 - Extend traits class to support custom logic after swaps and rotations [Open]

> Username: adolfogc  
> Created at: 2021-10-25 10:09:43 UTC  
> Updated at: 2021-10-25 10:09:43 UTC  
> Url: https://github.com/boostorg/intrusive/issues/69  

Hi,  
  
The Ygg library of intrusive data structures (https://github.com/tinloaf/ygg) allows custom logic to be called after node swaps and rotations by implementing the node traits class interface. For example:  
  
- Node traits: https://github.com/tinloaf/ygg/blob/ea8f04fdf8e5d19e048ba9f8d130bfda75863756/src/rbtree.hpp#L120  
- Implementation of the left rotation: https://github.com/tinloaf/ygg/blob/ea8f04fdf8e5d19e048ba9f8d130bfda75863756/src/rbtree.cpp#L318  
  
Do you see it possible to allow similar functionality in Boost.Instrusive by extending the traits class interface similarly to as in Ygg?
