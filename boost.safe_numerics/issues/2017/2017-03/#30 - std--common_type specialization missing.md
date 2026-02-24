# #30 - std::common_type specialization missing [Closed]

> Username: Corristo  
> Created at: 2017-03-04 20:18:14 UTC  
> Updated at: 2022-01-09 18:02:40 UTC  
> Closed at: 2018-08-14 21:57:27 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/30  

`std::common_type` is used in a variety of generic libraries, including `std::chrono`.  
Without a specialization for `safe<T>`s one cannot use the safe wrappers e.g. as a representation  
for `std::chrono::duration`.

---

## Comment 1

> Username: robertramey  
> Created at: 2018-08-14 21:57:27 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/30#issuecomment-413029517  

This looks interesting - but more complicated than it would appear.  My first instinct would be to look at specialization std::decay<safe<T> > -> T  and hope that it solves the common type issues.  If you know something about this (you certainly know more than I do), I'd appreciate hearing your thoughts.  For now I'm sort of under the gun to get this checked into official boost before CppCon so I'm putting this into the "Pending Issues list" in the documentation.

---

## Comment 2

> Username: codewiz  
> Created at: 2022-01-09 18:02:40 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/30#issuecomment-1008346192  

Would this be a good time to reconsider this issue?  
  
I'm evaluating `std::chrono::duration<safe<int32_t>, cpu_fast_tick>` to check for overflows on implicit conversions from wider duration types, and it won't work without specializing std::common_type for various combinations of safe and plain types.
