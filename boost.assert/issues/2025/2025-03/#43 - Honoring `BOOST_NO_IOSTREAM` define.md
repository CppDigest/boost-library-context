# #43 - Honoring `BOOST_NO_IOSTREAM` define [Closed]

> Username: krlydm  
> Created at: 2025-03-20 10:41:07 UTC  
> Updated at: 2025-03-24 13:50:05 UTC  
> Closed at: 2025-03-24 13:50:05 UTC  
> Url: https://github.com/boostorg/assert/issues/43  

I'm working on project where I'm trying to bump boost dependency from a old version (1.64) to one of the recent versions. My use case is a special one, because I have no iostream library available. This is causing the following error when I compile:   
```  
In file included from boost/exception/exception.hpp:9,  
boost/assert/source_location.hpp:137:33: error: ‘basic_ostream’ in namespace ‘_STL’ does not name a template type; did you mean ‘basic_string’?  
```  
This issue is originated to [this change ](https://github.com/boostorg/assert/commit/e52b379928ad3e1c52a07481037885f0d12bdfd3)when, operator<< is added for source_location in 1.73 release, so iostream is now required.  
  
Doing some research in other boost libraries, looks like other libraries are have option to use [`BOOST_NO_IOSTREAM`](https://github.com/search?q=org%3Aboostorg+BOOST_NO_IOSTREAM&type=code)for this use case when the iostream is not available. For example, [Boost.Optional library](https://github.com/boostorg/optional/commit/d0ab8004c016dc71366f6624856595713df061ae) has support for using `BOOST_NO_IOSTREAM`.  
  
Is it possible to support and honor the lack of iostream in Boost.Assert library too?

---

## Comment 1

> Username: pdimov  
> Created at: 2025-03-20 15:34:01 UTC  
> Url: https://github.com/boostorg/assert/issues/43#issuecomment-2740870956  

Does your system have `<iosfwd>`?

---

## Comment 2

> Username: krlydm  
> Created at: 2025-03-21 09:07:38 UTC  
> Updated at: 2025-03-21 09:09:07 UTC  
> Url: https://github.com/boostorg/assert/issues/43#issuecomment-2742752314  

Unfortunately that's the issue, there is no `<ostream>` or `<iosfwd>` available. Similar use case like [here](https://github.com/boostorg/detail/blob/a54b2619f8f75861836114d02a3fc70c7c9b3474/include/boost/blank.hpp#L18).

---

## Comment 3

> Username: pdimov  
> Created at: 2025-03-21 15:20:11 UTC  
> Url: https://github.com/boostorg/assert/issues/43#issuecomment-2743701180  

Can you please confirm that [this change](https://github.com/boostorg/assert/commit/5dcb2af5213ae132b7531e45e7f93258cc33ffd8) fixes things on your end?

---

## Comment 4

> Username: krlydm  
> Created at: 2025-03-24 12:37:58 UTC  
> Url: https://github.com/boostorg/assert/issues/43#issuecomment-2747988749  

Thanks for the super quick resolution on this! Issue is solved on my end by applying the linked commit.
