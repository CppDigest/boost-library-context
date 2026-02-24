# #2 Avoid unused typedef warnings with clang [Merged]

> Username: mgaunard  
> Created at: 2014-09-29 13:12:32 UTC  
> Updated at: 2014-10-02 17:28:18 UTC  
> Merged at: 2014-10-02 17:28:18 UTC  
> Closed at: 2014-10-02 17:28:18 UTC  
> Url: https://github.com/boostorg/static_assert/pull/2  

Clang generates warnings about unused typedefs when using BOOST_STATIC_ASSERT.  
The workaround for this already exists, but for some reason is restricted to GCC 4.7+.  
Since the 'unused' attribute has existed since the GCC 3.x days, we might as well enable it for any GCC 4.x, which also includes Clang, which is compatible with GCC 4.2.

---

## Comment 1

> Username: Flast  
> Created_at: 2014-09-30 02:53:17 UTC  
> Url: https://github.com/boostorg/static_assert/pull/2#issuecomment-57260643  

-Wunused-local-typedef was introduced at GCC4.7, however completely work with GCC4.8+.  
Refs [#7242](https://svn.boost.org/trac/boost/ticket/7242), [#8546](https://svn.boost.org/trac/boost/ticket/8546)  
  
IMHO, such macro should be provided by Boost.Config because any other libraries have same issue.  
see also [#8549](https://svn.boost.org/trac/boost/ticket/8549)

---
