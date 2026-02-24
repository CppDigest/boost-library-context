# #927 - specialize std::ranges::enable_borrowed_range for C++20? [Closed]

> Username: vinniefalco  
> Created at: 2025-09-27 23:45:51 UTC  
> Updated at: 2026-01-26 17:01:24 UTC  
> Closed at: 2026-01-26 17:01:24 UTC  
> Url: https://github.com/boostorg/url/issues/927  

Should we specialize `std::ranges::enable_borrowed_range` for the param and query views? Or are we doing it already? This indicates "if a range owns its elements or not"

---

## Comment 1

> Username: alandefreitas  
> Created at: 2025-09-30 03:53:02 UTC  
> Url: https://github.com/boostorg/url/issues/927#issuecomment-3349842476  

If I understand it correctly, the purpose of enable_borrowed_range is to indicate that the iterators remain valid even after the range's lifetime is over. In practice, this means the range is not only a view/ref but one where the iterators don't depend on any state of the view. I'm not sure if the latter is true and part of the library contract, but I suspect it is.
