# #75 Avoid `Wzero-as-null-pointer-constant` warning [Merged]

> Username: Flamefire  
> Created at: 2023-05-06 16:11:45 UTC  
> Updated at: 2023-05-11 06:57:12 UTC  
> Merged at: 2023-05-11 03:16:45 UTC  
> Closed at: 2023-05-11 03:16:45 UTC  
> Url: https://github.com/boostorg/mpl/pull/75  

Newer compilers may warn when using `0`, e.g.:  
```  
./boost/iterator/detail/facade_iterator_category.hpp:161:5: error: zero as null pointer constant [-Werror,-Wzero-as-null-pointer-constant]  
    BOOST_MPL_ASSERT_NOT((is_iterator_category<Traversal>));  
    ^  
./boost/mpl/assert.hpp:324:58: note: expanded from macro 'BOOST_MPL_ASSERT_NOT'  
              boost::mpl::assert_not_arg( (void (*) pred)0, 1 ) \  
```

---

## Comment 1

> Username: Flamefire  
> Created_at: 2023-05-07 09:32:34 UTC  
> Url: https://github.com/boostorg/mpl/pull/75#issuecomment-1537369813  

Appveyor build failure seems to be a transient github error

---
