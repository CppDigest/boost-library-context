# #182 - CMake inconsistency, use namespace Boost::callable_traits as library name [Closed]

> Username: HDembinski  
> Created at: 2019-04-13 20:37:23 UTC  
> Updated at: 2020-03-25 02:45:42 UTC  
> Closed at: 2020-03-25 02:45:42 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/182  

In the main CMakeLists.txt, I can find `add_library(callable_traits INTERFACE)` which is inconsistent with the conventions used by the 29 other Boost libraries that Boost::histogram (indirectly) depends on, see e.g. Boost::mp11, Boost::assert, Boost::config. They use the library name `Boost::something`.  
  
Please add the following line to provide a conforming alias  
`add_library(Boost::callable_traits ALIAS callable_traits)`

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-04-14 13:59:45 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/182#issuecomment-482982193  

There is another problem: the master CMakeLists.txt needs an  
`if(CMAKE_SOURCE_DIR STREQUAL CMAKE_CURRENT_SOURCE_DIR)` around the tests, so that they are not loaded if callable_traits is used as a dependency by another project. An example on how to do this is given by mp11, https://github.com/boostorg/mp11/blob/a03e644dc70facf7318432ce9bb86da9009d3944/CMakeLists.txt#L17

---

## Comment 2

> Username: badair  
> Created at: 2019-04-23 12:57:59 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/182#issuecomment-485791581  

Thanks! I'll get the fix merged in this week.

---

## Comment 3

> Username: HDembinski  
> Created at: 2019-04-23 13:04:36 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/182#issuecomment-485793884  

Great, I am looking forward to it. 👍

---

## Comment 4

> Username: badair  
> Created at: 2020-03-25 02:45:42 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/182#issuecomment-603609088  

Closing because the PR was merged...
