# #208 - Improving the API [Open]

> Username: JankoDedic  
> Created at: 2023-08-20 17:49:12 UTC  
> Updated at: 2023-11-09 22:03:49 UTC  
> Url: https://github.com/boostorg/unordered/issues/208  

First off, thank you for this amazing library! The recent performance improvements really inspire confidence and I was happy to replace my uses of `std::unordered_map` with a better alternative, without pulling in another library. I'm also very much looking forward to trying out the flat maps and concurrent maps!  
  
While the API compatibility with `std::unordered_map` is surely a valued feature by some and makes it easy to do a drop-in replacement, I cannot help but acknowledge that this exact API is bad, is widely recognized as bad, and leaves the users wanting more. API compatibility is a good feature for existing code, but it deprives new code of possible simplicity and clarity! Of course, these two goals aren't mutually exclusive: we could have both (in form of distinct types).  
  
While I am far from a domain expert in hash maps, there are some `std::unordered_map` API pitfalls and ergonomic issues that I face every day, and that have been echoed by the community for a long time.  
  
- It's a damn shame that `operator[]` has basically useless behavior for 99% of use cases, and it cannot simply be used for lookup and return something like `boost::optional<T&>`.  
- The use of `std::pair<iterator, bool>` as a return type of some functions is also another obvious ugly part that violates The Rule of When To Use `std::pair`.  
- The use of `std::pair<const Key, T>` as a `value_type` instead of a dedicated type like `map_entry<Key, Value>` with intuitively named members `key` and `value`.  
- A function like `insert` that takes a lambda parameter that doesn't get called if the element already exists in the map would certainly be nice to have (and is used often in other languages like Kotlin).  
- Having `map.keys()` and `map.values()` functions that return ranges of keys/values for easy iteration.  
  
These are just the first issues that come to my mind, but I'm sure that there are more. This talk by Chandler Carruth also gives some additional interesting ideas: https://www.youtube.com/watch?v=kye4aD-KvTU  
  
As I've said previously, I'm no expert on this and some or all of my suggestions may not be viable, worthwhile or good. They're just ideas off the top of my head and not exact requests. I just wanted to hopefully start a discussion towards something positive (because I haven't seen this being discussed) and hear what the maintainers think of this. Boost is trailblazing in this space when it comes to functionality and performance, and I strongly believe that it's in prime position to also deliver APIs that users will adore!  
  
Again, thank you for all your hard and amazing work! This is just my two cents on the issue, and I hope this will be at least slightly helpful in making this amazing library even better. 🙂

---

## Comment 1

> Username: justusranvier  
> Created at: 2023-11-09 22:03:48 UTC  
> Url: https://github.com/boostorg/unordered/issues/208#issuecomment-1804753781  

The ```equal_range``` function for ```multiset``` and ```multimap``` doesn't interoperate with ranges since it returns a pair of iterators.  
  
Maybe there should be an ```equal_subrange``` function which fixes that issue.
