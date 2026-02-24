# #86 - Export from namespace detail [Closed]

> Username: d7d1cd  
> Created at: 2025-10-22 06:10:25 UTC  
> Updated at: 2025-10-25 08:38:27 UTC  
> Closed at: 2025-10-25 08:38:27 UTC  
> Url: https://github.com/boostorg/multi_index/issues/86  

I'm using boost::multi_index_container in a C++20 module (included in GMF). In the module, I make the necessary use of the container and export it. In the code after importing the module, I get iterators from this container and try to compare them. I get a compilation error because the == operator isn't visible. To avoid this, I need to export boost::multi_index::detail::operator== in the module.  
  
I think it's wrong to export from detail. I believe the == operator (and others) should be in boost::multi_index, not detail.  
  
Here is an example: https://godbolt.org/z/xr1K8GaMc

---

## Comment 1

> Username: joaquintides  
> Created at: 2025-10-23 11:10:28 UTC  
> Url: https://github.com/boostorg/multi_index/issues/86#issuecomment-3436366453  

Hi, I accessed https://godbolt.org/z/xr1K8GaMc and it seems to build and run successfully?  
  
```  
Program returned: 0  
```

---

## Comment 2

> Username: d7d1cd  
> Created at: 2025-10-23 12:46:16 UTC  
> Url: https://github.com/boostorg/multi_index/issues/86#issuecomment-3436737202  

The launch was successful because the example exports the == operator. Here's an example without such an export: https://godbolt.org/z/GEfnMGqfs/  
  
My point is that using boost libraries in C++20 modules forces exporting entities from the detail namespace. Of course, exporting is necessary in any case. But it makes me feel a little guilty when I export something in my module that is in the detail namespace of someone else's library.  
  
For example, in a similar situation with the standard library, I export from the std namespace, which is perfectly legal. Here's an example: https://godbolt.org/z/v8s538WrE  
  
@joaquintides

---

## Comment 3

> Username: joaquintides  
> Created at: 2025-10-24 08:02:38 UTC  
> Url: https://github.com/boostorg/multi_index/issues/86#issuecomment-3441673624  

Ok, I understand the issue now. In my opinion, it's perfectly reasonable to export entities from `boost::multi_index::detail` if necessary: as it happens, MultiIndex iterators are defined in this namespace, which is totally valid as they are only reachable from the nested `iterator` typedef in the container and/or indices.  
  
At some point in the future, Boost.MultiIndex will provide the modules exports itself and you'll be spared thay levy. But currently this looks like very distant future TBH.
