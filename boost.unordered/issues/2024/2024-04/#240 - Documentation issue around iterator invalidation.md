# #240 - Documentation issue around iterator invalidation [Closed]

> Username: bgemmill  
> Created at: 2024-04-03 01:36:09 UTC  
> Updated at: 2024-04-03 18:52:28 UTC  
> Closed at: 2024-04-03 18:07:10 UTC  
> Url: https://github.com/boostorg/unordered/issues/240  

While unordered_node_map keeps valid pointer/references to keys, the iterators get invalidated on rehashing according to @joaquintides [here](https://github.com/boostorg/multi_index/issues/72#issuecomment-2032410652)   
  
The [documentation](https://www.boost.org/doc/libs/1_84_0/libs/unordered/doc/html/unordered.html#unordered_node_map) implies iterator stability:  
```boost::unordered_node_map uses an open-addressing layout like boost::unordered_flat_map, but, being node-based, it provides pointer/iterator stability and node handling functionalities.```  
  
It may be worth repeating how iterators are invalidated within the standards compliance [section](https://www.boost.org/doc/libs/1_84_0/libs/unordered/doc/html/unordered.html#compliance_open_addressing_containers) for open addressing containers as well.

---

## Comment 1

> Username: joaquintides  
> Created at: 2024-04-03 18:08:54 UTC  
> Url: https://github.com/boostorg/unordered/issues/240#issuecomment-2035273386  

Hi, fixed in docs, thanks for the report.  
  
> It may be worth repeating how iterators are invalidated within the standards compliance [section](https://www.boost.org/doc/libs/1_84_0/libs/unordered/doc/html/unordered.html#compliance_open_addressing_containers) for open addressing containers as well.  
  
I'm reluctant to add anything there because the section lists the _differences_ with respect to standard unordered associative containers, and the standard is very clear that iterator stability is not provided:  
  
https://eel.is/c++draft/unord.req.general#9

---

## Comment 2

> Username: bgemmill  
> Created at: 2024-04-03 18:52:27 UTC  
> Url: https://github.com/boostorg/unordered/issues/240#issuecomment-2035354257  

Thanks for closing this one out!
