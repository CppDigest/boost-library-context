# #88 - Implement C++17 MoveAssignable requirements for self-move assignments [Closed]

> Username: igaztanaga  
> Created at: 2018-11-12 13:41:25 UTC  
> Updated at: 2019-09-17 01:46:13 UTC  
> Closed at: 2019-04-30 16:05:57 UTC  
> Url: https://github.com/boostorg/container/issues/88  

In C++17, self-move assignment is no longer undefined behavior:  
  
MoveAssignable requirements [moveassignable]  
  
[ Note: rv must still meet the requirements of the library  
component that is using it, whether or not t and rv refer to the same object. The  
operations listed in those requirements must work as specified whether rv has been moved  
from or not. — end note ]  
  
All Boost.Container classes should be updated to guarantee MoveAssignable requirements

---

## Comment 1

> Username: Mike-Devel  
> Created at: 2019-03-07 05:33:31 UTC  
> Url: https://github.com/boostorg/container/issues/88#issuecomment-470391429  

Are you sure that self move is  allowed/required to work in c++17? That would be a breaking change. Can you post a link to the section?

---

## Comment 2

> Username: igaztanaga  
> Created at: 2019-03-13 21:29:49 UTC  
> Url: https://github.com/boostorg/container/issues/88#issuecomment-472613300  

According to the final draft before C++17 (http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2017/n4659.pdf):  
  
Section 20.5.3.1, Table 25 — MoveAssignable requirements [moveassignable]  
  
Expression: t = rv   
Return type: T&  
Return value: t  
Post-condition: If t and rv do not refer to the same object, t is equivalent to  
the value of rv before the assignment rv’s state is unspecified. [ Note: rv must still meet the requirements of the library component that is using it, whether or not t and rv refer to the same object. The operations listed in those requirements must work as specified whether rv has been moved from or not. — end note ]

---

## Comment 3

> Username: Mike-Devel  
> Created at: 2019-03-13 23:27:02 UTC  
> Url: https://github.com/boostorg/container/issues/88#issuecomment-472645420  

I guess you are right.  I read this as "even after self move assignment the object must still be in a valid state" (even if it isn't specified what that state is exactly).

---

## Comment 4

> Username: faithandbrave  
> Created at: 2019-07-08 04:54:34 UTC  
> Url: https://github.com/boostorg/container/issues/88#issuecomment-509078358  

FYI : https://wg21.cmeerw.net/lwg/issue2468

---

## Comment 5

> Username: linholmes  
> Created at: 2019-09-17 01:46:13 UTC  
> Url: https://github.com/boostorg/container/issues/88#issuecomment-532023233  

Will it influence the old C++ version of compiler，just like c++14 or c++11?
