# #71 Added implicit cast to core library : issue #49 [Closed]

> Username: anshu-khare-design  
> Created at: 2020-01-20 07:53:28 UTC  
> Updated at: 2020-01-20 17:02:59 UTC  
> Closed at: 2020-01-20 16:58:49 UTC  
> Url: https://github.com/boostorg/core/pull/71  

I have worked on issue #49  of core library.

---

## Comment 1

> Username: mloskot  
> Created_at: 2020-01-20 16:01:30 UTC  
> Updated_at: 2020-01-20 16:01:52 UTC  
> Url: https://github.com/boostorg/core/pull/71#issuecomment-576336262  

WTF 😨 Mate, leave `boostorg/core` alone, please!

---

## Comment 2

> Username: anshu-khare-design  
> Created_at: 2020-01-20 16:39:53 UTC  
> Url: https://github.com/boostorg/core/pull/71#issuecomment-576351962  

Implicit_cast typically means that we are able to convert one datatype to another.  
If this is wrong, could you please link me to relevant sources?

---

## Comment 3

> Username: mloskot  
> Created_at: 2020-01-20 16:49:21 UTC  
> Updated_at: 2020-01-20 16:53:11 UTC  
> Url: https://github.com/boostorg/core/pull/71#issuecomment-576355549  

> If this is wrong, could you please link me to relevant sources?  
  
Mind you you have been already told: https://lists.boost.org/Archives/boost/2020/01/247987.php  
  
The relevant place to look is the `implicit_cast.cpp` file you added to the library in this PR.   
Look at it again and review what you have added, what it is, if/how it integrates with the library.   
  
To me, this is a useless file `.cpp` file, a randomly dropped in rubbish.  
  
Answering those initial questions should already tell you it is a good idea to close this PR and forget, so I should not even make comments about quality of the code you submitted:  
  
- useless comments  
- not to mention `using namespace std`  
- useless as unused `#include <iostream>`  
- useless `#pragma once` in `.cpp` file, not to mention it is compiler-specific feature used without proper preprocessor conditional  
- useless header guard used in `.cpp` file, not to mention the guard does not follow the https://www.boost.org/development/header.html  
  
**If** you really really really die hard to contribute to `boostorg/core`, then _really really really try hard_ and spend time getting familiar with the current contents of the library, learn how it is structured, integrated, built (or perhaps it is not built, hint!), etc.  
  
Again, you have been already given plenty of guidelines https://lists.boost.org/Archives/boost/2020/01/247987.php or even more https://lists.boost.org/Archives/boost/2020/01/248004.php. Hint: the last one is golden!

---

## Comment 4

> Username: glenfe  
> Created_at: 2020-01-20 16:58:49 UTC  
> Url: https://github.com/boostorg/core/pull/71#issuecomment-576359133  

Everything about this is wrong.   
  
For your edification only, this is the implementation of `implicit_cast`:  
  
```  
template<class T>  
BOOST_CONSTEXPR inline T implicit_cast(typename type_identity<T>::type v)  
{  
    return v;  
}  
```  
We do not want to address #49, so I'm closing this PR.

---

## Comment 5

> Username: mloskot  
> Created_at: 2020-01-20 16:59:35 UTC  
> Url: https://github.com/boostorg/core/pull/71#issuecomment-576359408  

@glenfe Shall the #49 be closed as well?

---
