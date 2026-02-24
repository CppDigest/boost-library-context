# #141 - Support for Boost.MP11 and Boost.Hana type lists? [Closed]

> Username: rhalbersma  
> Created at: 2018-04-24 20:53:31 UTC  
> Updated at: 2019-03-07 21:30:07 UTC  
> Closed at: 2019-03-07 21:30:07 UTC  
> Url: https://github.com/boostorg/test/issues/141  

`BOOST_AUTO_TEST_CASE_TEMPLATE` uses Boost.MPL's machinery (in particular: `boost::mpl::for_each`) to automate tests cases over type lists. However, Boost.MPL is not compatible with the modern Boost.MP11 and Boost.Hana type lists, firing `BOOST_MPL_ASSERT(( is_sequence<Sequence> ));` when passing an `mp11::mp_list` or a `hana::tuple` instead of an `mpl::vector`.   
  
Is it possible to swap out the Boost.MPL implementation for Boost.MP11? I think Boost.MP11 uses only pattern matching on variadic parameter packs to determine if something is a type list, so it should be backward compatible with existing code that passes `mpl::vector` as type lists, or even with Boost.Hana (or `std::tuple`) type lists. To accomodate C++98 users, using an MP11 implementation can be done conditionally on the compiler version.

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2018-10-02 00:32:23 UTC  
> Url: https://github.com/boostorg/test/issues/141#issuecomment-426107853  

Indeed, we can easily remove the use of `mpl` in this case (sorry for the delayed answer).

---

## Comment 2

> Username: kedarbhat  
> Created at: 2019-02-15 19:42:33 UTC  
> Url: https://github.com/boostorg/test/issues/141#issuecomment-464174853  

Both mp11 and Hana provide facilities for use with MPL/Fusion: [Godbolt Compiler Explorer Example](https://gcc.godbolt.org/z/3nAGcE)

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2019-03-03 20:16:49 UTC  
> Url: https://github.com/boostorg/test/issues/141#issuecomment-469059687  

I have a possible implementation in the branch `topic/GH-141-mp11-hana-typelist`. Would you please give a try? The signature of the test declaration does not change.

---

## Comment 4

> Username: raffienficiaud  
> Created at: 2019-03-03 20:17:27 UTC  
> Url: https://github.com/boostorg/test/issues/141#issuecomment-469059734  

+CC @tonyelewis

---

## Comment 5

> Username: raffienficiaud  
> Created at: 2019-03-07 21:30:07 UTC  
> Url: https://github.com/boostorg/test/issues/141#issuecomment-470702310  

In master, should be in 1.70.
