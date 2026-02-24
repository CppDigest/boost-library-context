# #92 [CMake] Add minimal cmake file [Merged]

> Username: Mike-Devel  
> Created at: 2018-10-01 08:45:00 UTC  
> Updated at: 2018-11-05 01:08:00 UTC  
> Merged at: 2018-11-04 15:24:49 UTC  
> Closed at: 2018-11-04 15:24:50 UTC  
> Url: https://github.com/boostorg/type_traits/pull/92  

Generate cmake target that can be used by other libraries  
to express their dependency on this library and retrieve  
any configuration information such as the include directory,  
transitive dependencies, necessary compiler options or the  
required c++ standards level.

---

## Comment 1

> Username: Mike-Devel  
> Created_at: 2018-10-01 08:48:13 UTC  
> Url: https://github.com/boostorg/type_traits/pull/92#issuecomment-425832943  

Note that - while it doesn't do any harm - will not work until https://github.com/boostorg/static_assert/pull/6 is merged. This PR also doesn't add any tests for the cmake file itself.

---

## Comment 2

> Username: Mike-Devel  
> Created_at: 2018-10-28 13:29:53 UTC  
> Url: https://github.com/boostorg/type_traits/pull/92#issuecomment-433705432  

The cmake file has been added to the static_assert library, so this PR can be merged now.

---

## Comment 3

> Username: Mike-Devel  
> Created_at: 2018-11-05 01:07:57 UTC  
> Url: https://github.com/boostorg/type_traits/pull/92#issuecomment-435727054  

Thanks

---
