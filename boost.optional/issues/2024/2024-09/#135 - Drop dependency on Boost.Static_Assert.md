# #135 - Drop dependency on Boost.Static_Assert [Closed]

> Username: akrzemi1  
> Created at: 2024-09-28 06:55:38 UTC  
> Updated at: 2026-02-18 20:27:50 UTC  
> Closed at: 2026-02-18 20:27:49 UTC  
> Url: https://github.com/boostorg/optional/issues/135  

Currently Boost.Optional still depends on Boost.Static_Assert and uses `BOOST_STATIC_ASSERT` instead of `static_asseet`.  
  
This is due to an omission in the process. I depprecated (three releases ago) the support for certain C++11 workarounds ([see the list](https://github.com/boostorg/optional/blob/develop/include/boost/none_t.hpp#L18)), but I forgot about the `static_assert`. So while we now can use the listed C++11 features, it is still unsafe to use `static_assert`.  
  
Ultimately, we want to be able to use `static_assert` in Boost.Optional. This requires two steps.  
  
 1. Determine if there even exist compilers that support rvalue references, variadic templates, fuction ref-qualifiers, but do not support `static_assert`. consult the maintainers of Boost.Config. If there aren't such compilers, just drop the dependency on Boost.Static_Assert right away.  
 2. Start the three-release depprecation period for `BOOST_NO_CXX11_STATIC_ASSERT`.

---

## Comment 1

> Username: typenametea  
> Created at: 2024-10-04 20:12:29 UTC  
> Url: https://github.com/boostorg/optional/issues/135#issuecomment-2394499789  

What would be the best way to consult the maintainers, should I put a message out on the mailing list?

---

## Comment 2

> Username: akrzemi1  
> Created at: 2024-10-06 22:36:48 UTC  
> Url: https://github.com/boostorg/optional/issues/135#issuecomment-2395611327  

That would be one way. Or just ask on Slack.

---

## Comment 3

> Username: akrzemi1  
> Created at: 2026-02-18 20:27:49 UTC  
> Url: https://github.com/boostorg/optional/issues/135#issuecomment-3923044845  

Done
