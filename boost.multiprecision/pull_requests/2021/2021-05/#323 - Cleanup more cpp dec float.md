# #323 Cleanup more cpp dec float [Merged]

> Username: ckormanyos  
> Created at: 2021-05-10 09:19:56 UTC  
> Updated at: 2021-05-11 12:09:25 UTC  
> Merged at: 2021-05-11 10:00:52 UTC  
> Closed at: 2021-05-11 10:00:52 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/323  

This is a partial rework of #315.  
  
The new constructor of `cpp_dec_float` from `std::initializer_list` can cause all kinds of ambiguity problems. These include confusion with C++11 uniform initialization syntax and ambiguity with `cpp_dec_float`'s constructor from `std::string` (which also has construction from initializer list).  
  
In light of this, replace the private new constructor from initializer list with a (still not perfect) private convenience function called `from_lst`. Use this consistently within the internal `cpp_dec_float` code.

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2021-05-10 09:59:13 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/323#issuecomment-836496194  

Although potential constructor ambiguity problems were, in fact, shielded by the fronend `number` template, it is probably best to avoid constructors of the backend type from as many aggregate type(s) as possible.

---
