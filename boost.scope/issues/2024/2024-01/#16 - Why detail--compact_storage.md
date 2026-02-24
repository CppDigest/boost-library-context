# #16 - Why detail::compact_storage? [Closed]

> Username: grisumbras  
> Created at: 2024-01-25 18:29:31 UTC  
> Updated at: 2024-01-28 17:46:26 UTC  
> Closed at: 2024-01-28 17:46:25 UTC  
> Url: https://github.com/boostorg/scope/issues/16  

I forgot to ask this before the review, so I'm asking now: why do you have your own helper that employs EBO rather than using `boost::empty_value` from Core?

---

## Comment 1

> Username: Lastique  
> Created at: 2024-01-25 21:03:53 UTC  
> Url: https://github.com/boostorg/scope/issues/16#issuecomment-1910991877  

I wasn't aware of it when I wrote `compact_storage`. Now that I look at it, there are a few comments:  
  
- It doesn't provide `noexcept` transparency in constructors, which is important as it would affect `unique_resource` `noexcept`-ness.  
- I don't like that it includes `<utility>`.  
- Not sure why the `empty_init_t` extra argument is needed, although this is not a big problem.  
  
I suppose, I could switch to `empty_value` once it is updated to solve at least the first two issues.

---

## Comment 2

> Username: Lastique  
> Created at: 2024-01-28 17:46:25 UTC  
> Url: https://github.com/boostorg/scope/issues/16#issuecomment-1913672437  

Actually, it looks like `empty_value` only works for *empty* types while `compact_storage` works for all non-final classes. The difference is that even if the class is not empty, `compact_storage` allows to place new data members in its tail padding. Hence the "compact" in the name. So I won't be switching to `empty_value` after all.
