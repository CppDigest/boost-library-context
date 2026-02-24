# #12 - Should mp_from_sequence be a public algorithm? [Closed]

> Username: rhalbersma  
> Created at: 2017-08-21 19:25:07 UTC  
> Updated at: 2017-10-15 21:12:40 UTC  
> Closed at: 2017-10-15 21:12:40 UTC  
> Url: https://github.com/boostorg/mp11/issues/12  

I am trying to port some Boost.MPL style code to MP11. I have a `mpl::vector_c<int, 2, 3, 5, 7, 11>` and would like to write the equivalent MP11 code. From your [article](http://pdimov.com/cpp2/simple_cxx11_metaprogramming.html) I get that you could do `mp_from_sequence<mp_integer_sequence<int, 2, 3, 5, 7, 11>>`.   
  
However, it turns out that `mp_from_sequence` is currently in `namespace detail`, so I guess I cannot rely on this. Would it be possible to make `mp_from_sequence` a public algorithm? Or perhaps even better, I'd prefer to write directly something like `mp_list_c<int, 2, 3, 5, 7, 11>`. Or is there another helper to do what I want? Any thoughts?

---

## Comment 1

> Username: pdimov  
> Created at: 2017-08-27 18:19:24 UTC  
> Url: https://github.com/boostorg/mp11/issues/12#issuecomment-325215203  

Both suggestions (make `mp_from_sequence` public and add `mp_list_c`) are very sensible and I'll see about implementing them.

---

## Comment 2

> Username: pdimov  
> Created at: 2017-10-15 21:12:40 UTC  
> Url: https://github.com/boostorg/mp11/issues/12#issuecomment-336742051  

I've added `mp_from_sequence` and `mp_list_c`. Thanks for the suggestion.
