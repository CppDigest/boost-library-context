# #27 - Support for constructors [Open]

> Username: pdimov  
> Created at: 2021-12-05 17:22:49 UTC  
> Updated at: 2024-05-11 15:06:12 UTC  
> Url: https://github.com/boostorg/describe/issues/27  

`BOOST_DESCRIBE_CONSTRUCTORS(X, (int), (float, float), (X const&), (X&&))`, `mod_constructor`.  
  
At the moment the workaround is https://godbolt.org/z/TxYKs5Wfr, but it would be better to support this natively.

---

## Comment 1

> Username: RichardLuo0  
> Created at: 2024-05-10 18:50:01 UTC  
> Url: https://github.com/boostorg/describe/issues/27#issuecomment-2105107909  

I would like to have this feature. This can help me solve this: https://godbolt.org/z/GG1oK3vbj.  
Why is here no activity since 2022?

---

## Comment 2

> Username: pdimov  
> Created at: 2024-05-11 15:06:10 UTC  
> Url: https://github.com/boostorg/describe/issues/27#issuecomment-2105927213  

> Why is here no activity since 2022?  
  
I've been busy with other things, sorry.  
  
I'll try to get back to this before 1.86 ships.
