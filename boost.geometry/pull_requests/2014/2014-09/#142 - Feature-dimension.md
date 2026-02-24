# #142 Feature/dimension [Closed]

> Username: awulkiew  
> Created at: 2014-09-25 10:17:15 UTC  
> Updated at: 2014-10-08 13:50:25 UTC  
> Closed at: 2014-10-01 14:38:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/142  

Summary:  
- the dimension value type is always `std::size_t`, it no longer depends on the one used in `traits::dimension<>`,  
- the type of `Dimensions` template parameters of `assert_dimension...()` functions is changed to `std::size_t`  
  
PR related to the https://github.com/boostorg/geometry/pull/140.  
  
One thing I noticed is inconsistent(?) naming of `assert_dimension...()` functions. Functions `assert_dimension_less_equal()` and `assert_dimension_greater_equal()` takes Dimensions parameter, as well as function `assert_dimension()`. But the function `assert_dimension_equal()` takes only Geometries. For me `assert_dimension_equal()` and `assert_dimension()` names/interfaces should be swapped.  
  
Another thing, is it required to use `boost::mpl::equal_to` in assert_dimension() ? What's the reason for this? Could simple comparison `==` be done there to be consistent with the rest of the functions?

---

## Comment 1

> Username: awulkiew  
> Created_at: 2014-10-01 14:38:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/142#issuecomment-57473958  

It's not clear if this change would be beneficial so I'm closing this PR. I'll only apply changes to `index::detail::is_valid` and add a `MPL_ASSERT` for `traits::dimension<>::value > 0` in `dimension<>` specialization for Points to ensure safe conversion to unsigned integral type.

---
