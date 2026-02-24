# #22 Avoid unused parameter warning in BOOST_FUSION_DEFINE_STRUCT_INLINE [Merged]

> Username: klemensbaum  
> Created at: 2014-10-20 09:52:57 UTC  
> Updated at: 2014-10-20 09:58:07 UTC  
> Merged at: 2014-10-20 09:58:00 UTC  
> Closed at: 2014-10-20 09:58:00 UTC  
> Url: https://github.com/boostorg/fusion/pull/22  

Submitting this patch as a pull request, see also https://svn.boost.org/trac/boost/ticket/10679.  
  
Original description:  
Expanding the BOOST_FUSION_DEFINE_STRUCT_INLINE macro leads to unused parameter warnings about 'it1' and 'it2', even when the boost include path is marked with -isystem.  
  
The attached patch should avoid the warning.

---

## Comment 1

> Username: djowel  
> Created_at: 2014-10-20 09:58:07 UTC  
> Url: https://github.com/boostorg/fusion/pull/22#issuecomment-59717582  

Thanks!

---
