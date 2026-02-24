# #99 string_view: Add string_view::contains methods [Merged]

> Username: Lastique  
> Created at: 2022-05-02 22:28:51 UTC  
> Updated at: 2022-08-12 13:06:28 UTC  
> Merged at: 2022-05-31 08:53:31 UTC  
> Closed at: 2022-05-31 08:53:31 UTC  
> Url: https://github.com/boostorg/utility/pull/99  

These methods were added in C++23.  
  
Closes https://github.com/boostorg/utility/issues/93.  
  
@mclow Please take a look.

---

## Comment 1

> Username: glenfe  
> Created_at: 2022-05-03 00:42:42 UTC  
> Url: https://github.com/boostorg/utility/pull/99#issuecomment-1115504809  

It might be trivial, but I'd still prefer some unit tests.

---

## Comment 2

> Username: Lastique  
> Created_at: 2022-05-03 21:33:04 UTC  
> Url: https://github.com/boostorg/utility/pull/99#issuecomment-1116681131  

Agreed. Added tests and made a few corrections to the existing tests.

---

## Comment 3

> Username: mclow  
> Created_at: 2022-05-03 21:33:48 UTC  
> Url: https://github.com/boostorg/utility/pull/99#issuecomment-1116681781  

Im at C++Now at the moment ; I'll try to look at this, but it ,ay be next week

---

## Review 4 [Commented]

> Username: mclow  
> Created_at: 2022-05-30 20:33:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/utility/pull/99#pullrequestreview-989667522  

This looks OK to me; the additions are fine.  I have a couple of nits, and frankly, the `size_t` -> `std::size_t` changes were distracting.  
  
Let me know what you think about `rbegin`

📁 test/string_ref_test2.cpp

```diff
  58 |         BOOST_TEST ( sr.starts_with ( foo ));
  57 |-         --p;
  59 |+         foo.erase ( foo.end () - 1 );
```

> Username: mclow  
> Created_at: 2022-05-30 20:30:58 UTC  
> Updated_at: 2022-05-30 20:33:46 UTC  
> Url: https://github.com/boostorg/utility/pull/99#discussion_r885067445  

I think that `foo.erase(foo.rbegin());` would be more idiomatic.


📁 test/string_view_test2.cpp

```diff
  84 |+         BOOST_TEST ( sr.contains ( foo ));
  85 |+         if ( ( foo.size () & 1u ) != 0u )
  86 |+             foo.erase ( foo.end () - 1 );
```

> Username: mclow  
> Created_at: 2022-05-30 20:31:34 UTC  
> Updated_at: 2022-05-30 20:33:46 UTC  
> Url: https://github.com/boostorg/utility/pull/99#discussion_r885067629  

Here even more so - `rbegin()`


---

## Comment 5

> Username: Lastique  
> Created_at: 2022-05-30 21:41:42 UTC  
> Url: https://github.com/boostorg/utility/pull/99#issuecomment-1141492587  

> Let me know what you think about `rbegin`  
  
`std::string` does not have `erase` by `reverse_iterator`. You would have to `foo.erase(foo.rbegin().base() - 1)`, which I don't think is any better.

---

## Comment 6

> Username: mclow  
> Created_at: 2022-05-30 23:53:03 UTC  
> Url: https://github.com/boostorg/utility/pull/99#issuecomment-1141544972  

> > Let me know what you think about `rbegin`  
>   
> `std::string` does not have `erase` by `reverse_iterator`.   
  
How embarrassing.. My bad. I withdraw the suggestion.

---

## Comment 7

> Username: Lastique  
> Created_at: 2022-05-31 08:53:24 UTC  
> Url: https://github.com/boostorg/utility/pull/99#issuecomment-1141859265  

Ok, merging then. Thanks for the review.

---
