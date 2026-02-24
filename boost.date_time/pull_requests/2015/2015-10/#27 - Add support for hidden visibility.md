# #27 Add support for hidden visibility [Merged]

> Username: Lastique  
> Created at: 2015-10-04 19:21:11 UTC  
> Updated at: 2016-12-03 11:01:55 UTC  
> Merged at: 2016-12-03 07:11:50 UTC  
> Closed at: 2016-12-03 07:11:50 UTC  
> Url: https://github.com/boostorg/date_time/pull/27  

The pull request fixes https://svn.boost.org/trac/boost/ticket/11707, as well as a few other issues:  
- All locale facets are marked visible.  
- All date and time types, as well as other associated types also marked visible. This is required for facets to be visible because these types are used as template parameters (gcc requires that all types in template parameters also have default visibility).  
- All exception types are also marked visible.  
- Converted tabs to spaces.

---

## Comment 1

> Username: eldiener  
> Created_at: 2015-10-17 04:29:05 UTC  
> Url: https://github.com/boostorg/date_time/pull/27#issuecomment-148885054  

I will look at this but I want to be sure this fixes problems and does not create any further ones.

---

## Comment 2

> Username: Lastique  
> Created_at: 2015-11-09 22:14:07 UTC  
> Url: https://github.com/boostorg/date_time/pull/27#issuecomment-155215829  

Any updates?

---

## Comment 3

> Username: amerry  
> Created_at: 2015-11-13 13:56:46 UTC  
> Url: https://github.com/boostorg/date_time/pull/27#issuecomment-156436421  

I've run into this issue, and this patch solves it for me.  
  
You appear to have some unrelated whitespace changes in the patch, though - I'm not sure if that was intentional, since that file is otherwise untouched.

---

## Comment 4

> Username: Lastique  
> Created_at: 2015-11-13 15:18:49 UTC  
> Url: https://github.com/boostorg/date_time/pull/27#issuecomment-156460281  

The whitespace changes are intentional - it's highlighted in the description.

---

## Comment 5

> Username: mclow  
> Created_at: 2016-11-30 15:55:07 UTC  
> Url: https://github.com/boostorg/date_time/pull/27#issuecomment-263910257  

This looks straightforward to me.  @eldiener, are you OK with this?

---

## Comment 6

> Username: Lastique  
> Created_at: 2016-12-03 11:01:55 UTC  
> Url: https://github.com/boostorg/date_time/pull/27#issuecomment-264632441  

Thanks.

---
