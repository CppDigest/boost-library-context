# #109 - Require C++11? [Closed]

> Username: pdimov  
> Created at: 2023-03-21 18:36:41 UTC  
> Updated at: 2024-09-13 10:50:12 UTC  
> Closed at: 2024-09-13 10:50:12 UTC  
> Url: https://github.com/boostorg/optional/issues/109  

A number of libraries have [announced that they will require C++11 starting from 1.84](https://www.boost.org/users/history/version_1_82_0.html).  
  
Will Optional do that as well? (Such deprecations need to be announced two releases in advance.)  
  
This will allow simplifications and enable getting rid of some of the current dependencies.

---

## Comment 1

> Username: akrzemi1  
> Created at: 2023-04-23 18:00:49 UTC  
> Url: https://github.com/boostorg/optional/issues/109#issuecomment-1519122170  

I have no plans to drop support for C++03, as I have no strong motivation.  
Indeed, the implementation of Boost.Optional would be much simpler if I required C++11, but I did not find this alone sufficient a motivation.  
Is leaving Boost.Optional as is causing problems for other libraries?

---

## Comment 2

> Username: pdimov  
> Created at: 2023-04-23 18:07:10 UTC  
> Url: https://github.com/boostorg/optional/issues/109#issuecomment-1519123448  

No serious problems as such.  
  
The gain would mostly come as reduced number of dependencies. Currently Boost.Optional has [12 dependencies](https://pdimov.github.io/boostdep-report/develop/module-weights.html#weight:12) which is sometimes an impediment for other (C++11 but pre-C++17) libraries that could use it in their interfaces.

---

## Comment 3

> Username: akrzemi1  
> Created at: 2023-10-01 09:56:31 UTC  
> Url: https://github.com/boostorg/optional/issues/109#issuecomment-1742027219  

Regarding the dependencies, Boost.Optional depends on Boost.Utility because it provides the InPlaceFactory and OptionalPoinees. This is a piece of functionality, so I would have to drop some portions of the interface. InPlaceFactory is not needed in C++11 anymore, but might break somebody's code. OptionalPointees is implementaiton detail, but it followed a good practice of abstracting the concepts for reuse in other components.

---

## Comment 4

> Username: akrzemi1  
> Created at: 2023-10-01 10:28:07 UTC  
> Url: https://github.com/boostorg/optional/issues/109#issuecomment-1742035308  

https://github.com/boostorg/optional/commit/bf58a919dfd7abe0d79ad802a4c93791a05aebd5

---

## Comment 5

> Username: akrzemi1  
> Created at: 2024-09-12 20:58:20 UTC  
> Url: https://github.com/boostorg/optional/issues/109#issuecomment-2347228404  

Dropped support for C++03 in develop.

---

## Comment 6

> Username: akrzemi1  
> Created at: 2024-09-13 10:50:12 UTC  
> Url: https://github.com/boostorg/optional/issues/109#issuecomment-2348645123  

And now in master.
