# #261 - End iterators are not dereferencable [Closed]

> Username: psiha  
> Created at: 2023-12-18 18:50:04 UTC  
> Updated at: 2024-09-30 21:19:32 UTC  
> Closed at: 2024-09-30 21:19:18 UTC  
> Url: https://github.com/boostorg/container/issues/261  

E.g.  
`boost::movelib::iterator_to_raw_pointer(seq.end())`  
in flat_tree.hpp [asserts at runtime when used with checked/'secure' containers](https://github.com/boostorg/container/issues/260).

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-09-30 21:19:31 UTC  
> Url: https://github.com/boostorg/container/issues/261#issuecomment-2384163409  

Many thanks for the report.
