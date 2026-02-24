# #46 Instantiate lexers with std::string, not flex_string [Merged]

> Username: jefftrull  
> Created at: 2019-05-03 15:16:20 UTC  
> Updated at: 2020-01-12 06:02:40 UTC  
> Merged at: 2020-01-12 06:02:36 UTC  
> Closed at: 2020-01-12 06:02:36 UTC  
> Url: https://github.com/boostorg/wave/pull/46  

Two samples fail at the link stage looking for a different `new_lexer_gen<>::new_lexer()` than we provide.  
  
AFAICT the type of the lexer is dependent on what string was used to construct the wave context object and that propagates down. Updating these files fixes the error.

---

## Comment 1

> Username: jefftrull  
> Created_at: 2019-05-03 18:23:42 UTC  
> Url: https://github.com/boostorg/wave/pull/46#issuecomment-489193420  

I think this resolves [TRAC 6588](https://svn.boost.org/trac10/ticket/6588)

---

## Review 2 [Approved]

> Username: hkaiser  
> Created_at: 2019-05-03 23:05:19 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/46#pullrequestreview-233702008  

LGTM.

---
