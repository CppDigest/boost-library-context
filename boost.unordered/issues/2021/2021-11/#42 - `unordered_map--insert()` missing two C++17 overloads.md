# #42 - `unordered_map::insert()` missing two C++17 overloads [Closed]

> Username: cmazakas  
> Created at: 2021-11-19 19:29:55 UTC  
> Updated at: 2022-02-11 23:09:11 UTC  
> Closed at: 2022-02-11 23:09:11 UTC  
> Url: https://github.com/boostorg/unordered/issues/42  

https://en.cppreference.com/w/cpp/container/unordered_map/insert  
  
The `unordered_map` implementation is missing `insert` overloads:  
```cpp  
insert_return_type insert(node_type&& nh);  
iterator insert(const_iterator hint, node_type&& nh);  
```

---

## Comment 1

> Username: cmazakas  
> Created at: 2022-02-11 23:09:11 UTC  
> Url: https://github.com/boostorg/unordered/issues/42#issuecomment-1036734346  

These two do exist in the implementation. Closing.
