# #323 Classic: Fix compilation with BOOST_DISABLE_THREADS defined [Merged]

> Username: Kojoley  
> Created at: 2017-12-14 18:28:57 UTC  
> Updated at: 2017-12-15 13:21:18 UTC  
> Merged at: 2017-12-15 13:21:15 UTC  
> Closed at: 2017-12-15 13:21:15 UTC  
> Url: https://github.com/boostorg/spirit/pull/323  

Closes https://svn.boost.org/trac10/ticket/12639  
  
Spirit should not include any Boost.Thread header until user explicitly  
requested threadsafe support with BOOST_SPIRIT_THREADSAFE defined.

---

## Comment 1

> Username: djowel  
> Created_at: 2017-12-14 23:58:54 UTC  
> Url: https://github.com/boostorg/spirit/pull/323#issuecomment-351872824  

Agreed! Indeed.

---
