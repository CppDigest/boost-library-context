# #54 - *set.rbegin() looks like O(log(N)) [Closed]

> Username: XeCycle  
> Created at: 2020-11-10 09:30:19 UTC  
> Updated at: 2020-11-13 21:57:13 UTC  
> Closed at: 2020-11-13 21:56:14 UTC  
> Url: https://github.com/boostorg/intrusive/issues/54  

[This commit](https://github.com/boostorg/intrusive/commit/6a9fa390aed83a8e8c1b1ef92f3b2f6cb5e93bce#diff-1d08730f04a8a30388466fc45aec4fbd7037f47c3c050cee82aab373d300ea6cL72) from 2015 flipped its implementation, but the commit message does not seem relevant.  So we can indeed support O(1) `back()`-like operation, but there's no dedicated `back()` and this one became O(log(N)).  I'm not sure whether that's an intentional change to conform to other changes, or just a mistake?  
  
(The [diagram comment](https://github.com/boostorg/intrusive/blob/develop/include/boost/intrusive/bstree_algorithms.hpp#L109) says the header's right child always points to rightmost node.)

---

## Comment 1

> Username: igaztanaga  
> Created at: 2020-11-13 21:57:13 UTC  
> Url: https://github.com/boostorg/intrusive/issues/54#issuecomment-727054842  

Thanks for the report!
