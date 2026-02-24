# #204 - Inconsistent noexcept-ness of static_vector::reserve [Closed]

> Username: Ukilele  
> Created at: 2021-12-18 17:13:35 UTC  
> Updated at: 2022-01-07 10:15:41 UTC  
> Closed at: 2022-01-07 10:15:41 UTC  
> Url: https://github.com/boostorg/container/issues/204  

According to the documentation of [static_vector](https://www.boost.org/doc/libs/1_78_0/doc/html/boost/container/static_vector.html), its member function `reserve` should be `noexcept`. But in fact it is not. The documentation is probably generated from the function declaration at [line 540](https://github.com/boostorg/container/blob/develop/include/boost/container/static_vector.hpp#L540). But this code is only defined when the macro `BOOST_CONTAINER_DOXYGEN_INVOKED` is defined. When actually calling the function, the overload from the base class (`boost::container::vector<...>`) gets called, which is defined in [vector.hpp, line 1567](https://github.com/boostorg/container/blob/develop/include/boost/container/vector.hpp#L1567).  
  
I would be willing to provide a pull request with a fix, but I am not sure what needs to be fixed.  
The first option would be to not mention `noexcept` in the documentation, i.e. remove the `BOOST_NOEXCEPT_OR_NOTHROW` from [line 540](https://github.com/boostorg/container/blob/develop/include/boost/container/static_vector.hpp#L540). The second option would be to move the `reserve`-function at line 540 out of the `BOOST_CONTAINER_DOXYGEN_INVOKED`-block and to always provide an actual definition of it.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-12-18 17:15:31 UTC  
> Updated at: 2021-12-18 17:15:40 UTC  
> Url: https://github.com/boostorg/container/issues/204#issuecomment-997233029  

> I'm not sure that reserve() can be a no-op. what happens if you try to reserve more than there's capacity? it needs to fail.

---

## Comment 2

> Username: igaztanaga  
> Created at: 2022-01-07 10:15:20 UTC  
> Url: https://github.com/boostorg/container/issues/204#issuecomment-1007291263  

Thanks for the report. Throwing on capacity exhaustion depends on the options used by the container (by default it throws when static capacity is exceeded) so the documentation should reflect this.
