# #235 - unordered_map iterator stability after reserve [Closed]

> Username: GuyAv46  
> Created at: 2024-02-18 12:46:39 UTC  
> Updated at: 2024-02-19 08:58:58 UTC  
> Closed at: 2024-02-19 08:58:58 UTC  
> Url: https://github.com/boostorg/unordered/issues/235  

I was trying to replace the standard unordered_map in my code with the boost implementation, but I ran into an instability that caused my test to crash.  
  
I was able to find [minimal reproduction steps](https://godbolt.org/z/KGx9xx6bE). Notice that by replacing the `boost::unordered_map` with `std::unordered_map` at the start, the program succeeds.  
  
I couldn't find any documentation about this, but I was under the impression that Boost's unordered map is a drop-in replacement for the std one.

---

## Comment 1

> Username: cmazakas  
> Created at: 2024-02-18 23:07:39 UTC  
> Url: https://github.com/boostorg/unordered/issues/235#issuecomment-1951477184  

All iterators are invalidated after calls to rehash() and reserve().  
  
See the section here on cppreference: https://en.cppreference.com/w/cpp/container/unordered_map#Iterator_invalidation

---

## Comment 2

> Username: GuyAv46  
> Created at: 2024-02-19 08:58:58 UTC  
> Url: https://github.com/boostorg/unordered/issues/235#issuecomment-1951982488  

Thanks!
