# #100 Change aligned_storage::dummy::data to unsigned char[] [Merged]

> Username: pdimov  
> Created at: 2022-02-18 19:58:45 UTC  
> Updated at: 2022-03-01 19:17:10 UTC  
> Merged at: 2022-03-01 19:17:09 UTC  
> Closed at: 2022-03-01 19:17:10 UTC  
> Url: https://github.com/boostorg/optional/pull/100  

The standard says in https://eel.is/c++draft/basic.memobj#intro.object-3 that arrays of `unsigned char` or arrays of `std::byte` can _provide storage_, but it doesn't say anything about arrays of `char`.  
  
To be on the safe side, we should use `unsigned char` in `aligned_storage`.  
  
(For context, please see thread starting at https://lists.boost.org/Archives/boost/2022/02/252503.php.)  
  
There's the separate question of whether returning the address of `dummy_`, as done in the may-alias case, is correct per the standard. It probably isn't, but since we're marking the union with may-alias, we're outside the standard proper, so it's not possible to tell. I haven't touched this part.

---
