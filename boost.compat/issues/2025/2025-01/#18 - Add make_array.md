# #18 - Add make_array [Closed]

> Username: cmorve-te  
> Created at: 2025-01-18 21:30:34 UTC  
> Updated at: 2025-01-24 09:06:47 UTC  
> Closed at: 2025-01-24 09:06:31 UTC  
> Url: https://github.com/boostorg/compat/issues/18  

Never really "added in later C++ standards". But since you can't add deduction guides for std::array pre-C++17, implementing https://en.cppreference.com/w/cpp/experimental/make_array is the closest that can be done.

---

## Comment 1

> Username: pdimov  
> Created at: 2025-01-24 01:46:59 UTC  
> Url: https://github.com/boostorg/compat/issues/18#issuecomment-2611370664  

We have `to_array`, and `make_array( 1, 2, 3, 4, 5 )` is very similar to what can be achieved today as `to_array({ 1, 2, 3, 4, 5 })`.

---

## Comment 2

> Username: cmorve-te  
> Created at: 2025-01-24 09:06:31 UTC  
> Updated at: 2025-01-24 09:06:47 UTC  
> Url: https://github.com/boostorg/compat/issues/18#issuecomment-2612015454  

Oh, silly me. I read the description saying "built-in array"/"C array", didn't reach the examples section, and didn't think you could use an initializer_list.  
  
Yeah, to_array works for me.
