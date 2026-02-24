# #313 X3: Fixed include guard names collision [Merged]

> Username: Kojoley  
> Created at: 2017-12-05 14:02:47 UTC  
> Updated at: 2017-12-06 10:51:57 UTC  
> Merged at: 2017-12-06 10:51:53 UTC  
> Closed at: 2017-12-06 10:51:54 UTC  
> Url: https://github.com/boostorg/spirit/pull/313  

Closes #257 and https://svn.boost.org/trac10/ticket/12084

---

## Comment 1

> Username: octopus-prime  
> Created_at: 2017-12-05 14:50:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/313#issuecomment-349326714  

Should we change the include guard names for qi too?  
  
Example (include/boost/spirit/home/qi/action/action.hpp):  
```cpp  
#if !defined(SPIRIT_ACTION_JANUARY_07_2007_1128AM)  
#define SPIRIT_ACTION_JANUARY_07_2007_1128AM  
```  
too  
```cpp  
#if !defined(BOOST_SPIRIT_QI_ACTION_JANUARY_07_2007_1128AM)  
#define BOOST_SPIRIT_QI_ACTION_JANUARY_07_2007_1128AM  
```  
?

---

## Comment 2

> Username: djowel  
> Created_at: 2017-12-05 15:35:25 UTC  
> Url: https://github.com/boostorg/spirit/pull/313#issuecomment-349340991  

It should be fine.

---
