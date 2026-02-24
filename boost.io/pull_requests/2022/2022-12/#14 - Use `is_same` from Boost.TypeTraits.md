# #14 Use `is_same` from Boost.TypeTraits [Merged]

> Username: Lastique  
> Created at: 2022-12-22 22:21:07 UTC  
> Updated at: 2022-12-23 13:55:37 UTC  
> Merged at: 2022-12-23 12:55:33 UTC  
> Closed at: 2022-12-23 12:55:33 UTC  
> Url: https://github.com/boostorg/io/pull/14  

`boost::core::is_same` is deprecated, so use the one from Boost.TypeTraits.

---

## Review 1 [Changes requested]

> Username: glenfe  
> Created_at: 2022-12-22 23:21:13 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/io/pull/14#pullrequestreview-1228372060  

📁 test/ostream_joiner_test.cpp

```diff
  15 |-     BOOST_TEST((boost::core::is_same<char,
  16 |-         boost::io::ostream_joiner<const char*>::char_type>::value));
  16 |+     BOOST_TEST_TRAIT_TRUE((boost::is_same<char,
```

> Username: glenfe  
> Created_at: 2022-12-22 23:21:07 UTC  
> Updated_at: 2022-12-22 23:21:24 UTC  
> Url: https://github.com/boostorg/io/pull/14#discussion_r1055923927  

There was a reason I didn't use `BOOST_TEST_TRAIT_TRUE`  because I use `<warnings>pedantic` and C++03 doesn't like it.  
  
Also, we will need to add  `type_traits` to the CI configurations, or they will fail. They deliberately don't use `depinst` because I want to be aware of what dependencies I have, even for testing.

> Username: Lastique  
> Created_at: 2022-12-22 23:35:39 UTC  
> Url: https://github.com/boostorg/io/pull/14#discussion_r1055931071  

The tests don't generate any warnings in C++03 on my machine. I've updated the CI configs, let's see how it goes.

> Username: glenfe  
> Created_at: 2022-12-22 23:56:20 UTC  
> Updated_at: 2022-12-22 23:56:21 UTC  
> Url: https://github.com/boostorg/io/pull/14#discussion_r1055938854  

As I see in aa70414fde0c3b337dcaa849b61241efd581a5f4 I explicitly switched away from all `lightweight_test_trait.hpp` when I enabled pedantic, so I'd like to keep it as it is.

> Username: Lastique  
> Created_at: 2022-12-23 00:54:09 UTC  
> Updated_at: 2022-12-23 00:54:10 UTC  
> Url: https://github.com/boostorg/io/pull/14#discussion_r1055959295  

You used `BOOST_TEST_TRAIT_SAME` back then, not `BOOST_TEST_TRAIT_TRUE`. The latter does not use variadic macros and is strictly C++03 compliant. You can see the latest CI does not produce warnings. Do you really want to revert back to `BOOST_TEST`? Please confirm.

> Username: glenfe  
> Created_at: 2022-12-23 03:13:12 UTC  
> Updated_at: 2022-12-23 03:13:13 UTC  
> Url: https://github.com/boostorg/io/pull/14#discussion_r1056007950  

Yes, please. Let this change only be about switching from Core to TypeTraits for is_same. (Thanks)

> Username: Lastique  
> Created_at: 2022-12-23 11:37:41 UTC  
> Url: https://github.com/boostorg/io/pull/14#discussion_r1056273536  

Ok, done.


---

## Comment 2

> Username: Lastique  
> Created_at: 2022-12-23 11:45:03 UTC  
> Url: https://github.com/boostorg/io/pull/14#issuecomment-1363884355  

I should note that GHA has [removed](https://github.com/actions/runner-images/issues/4312) windows-2016 image, so the CI will never complete.

---

## Comment 3

> Username: glenfe  
> Created_at: 2022-12-23 12:55:53 UTC  
> Url: https://github.com/boostorg/io/pull/14#issuecomment-1363930799  

Thanks Andrey. I'll update the CI configurations soon.

---
