# #61 Fix `bstree_algorithms<>::swap_nodes` [Merged]

> Username: dmsteck  
> Created at: 2021-05-13 22:47:43 UTC  
> Updated at: 2021-06-20 00:03:52 UTC  
> Merged at: 2021-06-20 00:03:38 UTC  
> Closed at: 2021-06-20 00:03:38 UTC  
> Url: https://github.com/boostorg/intrusive/pull/61  

The node swapping code (which is marked experimental) seems to lack special handling for the case when the nodes to be swapped are siblings (i.e., they have the same parent).  
  
In that case, special care needs to be taken when updating the parent node as aliasing occurs in the normal code.  
  
Run the test case from this PR against current `develop` to see the bug.  
  
### Changes in this PR  
  
* Fix the node swapping code by adding special logic for the sibling case  
* Moved all the parent updating code to the end of the function for clarity  
* Tests  
  
### Notes  
  
I've tried to keep everything C++03 and the tests build with `g++ -std=c++03` on my setup.  
  
I've also added (C) notices to both files; please let me know if this is in line with contribution guidelines. I'm not religious about them.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2021-06-20 00:03:51 UTC  
> Url: https://github.com/boostorg/intrusive/pull/61#issuecomment-864478241  

Many thanks for the report and the patch!

---
