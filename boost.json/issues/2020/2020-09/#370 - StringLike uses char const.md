# #370 - StringLike uses char const* [Closed]

> Username: vinniefalco  
> Created at: 2020-09-19 18:52:49 UTC  
> Updated at: 2020-09-24 01:34:53 UTC  
> Closed at: 2020-09-24 01:34:52 UTC  
> Url: https://github.com/boostorg/json/issues/370  

And since we're on this page, it's odd that the StringLike requirement wants construction from `(char*, std::size_t)` - pointer to non-const characters. I don't think that any string type has such a constructor, they all take `char const*`.

---

## Comment 1

> Username: sdkrystian  
> Created at: 2020-09-21 16:58:29 UTC  
> Updated at: 2020-09-21 16:59:41 UTC  
> Url: https://github.com/boostorg/json/issues/370#issuecomment-696242269  

I wrote it this way to provide maximum flexibility (such as converting to `std::span<char>`, and then being able to modify the underlying characters). However, since the `value` passed to `value_to` is `const` anyways, I suppose this doesn't make much sense.
