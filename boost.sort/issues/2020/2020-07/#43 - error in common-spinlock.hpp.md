# #43 - error in common/spinlock.hpp [Closed]

> Username: sandunder  
> Created at: 2020-07-19 12:05:09 UTC  
> Updated at: 2021-04-10 10:28:29 UTC  
> Closed at: 2020-07-20 01:51:44 UTC  
> Url: https://github.com/boostorg/sort/issues/43  

in boost/sort/common/spinlock.hpp, line 72, here is a "not", because of this, it couldn`t compile successfully.  
  
`    bool try_lock ( ) noexcept  
    {  
        return not af.test_and_set (std::memory_order_acquire);  
    };  
`

---

## Comment 1

> Username: Morwenn  
> Created at: 2020-07-19 19:03:21 UTC  
> Url: https://github.com/boostorg/sort/issues/43#issuecomment-660692037  

As a workaround your can `#include <ciso646>` before the Boost.Sort header, but that does sound like a problem worth fixing.

---

## Comment 2

> Username: sandunder  
> Created at: 2020-07-20 01:51:41 UTC  
> Url: https://github.com/boostorg/sort/issues/43#issuecomment-660755031  

It can fix the problem,thanks!

---

## Comment 3

> Username: fjtapia  
> Created at: 2020-07-20 14:53:19 UTC  
> Url: https://github.com/boostorg/sort/issues/43#issuecomment-661089744  

I am preparing the Windows Virtual Machine to install the last version of  
Visual Studio and compile all again.  
  
Visual C ++ is not intended to follow the C ++ standard, it is  
intended to be followed by the C ++ standard. That makes them stupid  
and annoying.  
  
I compiled the code with GCC and CLANG, without any problems  
  
But with Visual C ++ I had to include <ciso646> in a multitude of  
files. From what I see, with the new versions, instead of going  
forward, we go backwards.  
Distinguishing yourself by stupidities is a sign of low intelligence,  
and a strange way of gaining followers by making them waste their time  
with stupidities.  
  
I hope to have all resolved in one or two days. Thanks to all by your interest  
  
Francisco  
  
  
El lun., 20 jul. 2020 a las 3:51, sandunder (<notifications@github.com>)  
escribió:  
  
> Closed #43 <https://github.com/boostorg/sort/issues/43>.  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/43#event-3563516854>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AA5O5GA5CR2U7SSY5PGTSXDR4OPL5ANCNFSM4PBIPEOA>  
> .  
>

---

## Comment 4

> Username: fjtapia  
> Created at: 2020-07-23 11:32:08 UTC  
> Url: https://github.com/boostorg/sort/issues/43#issuecomment-662955713  

Please,  
could you say me wich version of Visual C++ are you using ?  
  
I just compiled with Visual Studio Community 2019. Version 16.6.5 and don't  
have any error compiling the code.  
Thanks  
  
Francisco  
  
El dom., 19 jul. 2020 a las 14:05, sandunder (<notifications@github.com>)  
escribió:  
  
> in boost/sort/common/spinlock.hpp, line 72, here is a "not", because of  
> this, it couldnt compile successfully. bool try_lock ( ) noexcept  
> {  
> return not af.test_and_set (std::memory_order_acquire);  
> };  
> `  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/43>, or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AA5O5GFCP22ABIT6IFBGQ7LR4LOQFANCNFSM4PBIPEOA>  
> .  
>

---

## Comment 5

> Username: hendrikmuhs  
> Created at: 2021-04-10 10:28:28 UTC  
> Url: https://github.com/boostorg/sort/issues/43#issuecomment-817114147  

I can reproduce the problem, `#include <ciso646>` fixes it. As long as it is included somewhere before the boost includes, it fixes the problem. Nevertheless this is not a clean solution.  
  
@fjtapia The reason why e.g. tests aren't affected: https://github.com/boostorg/sort/blob/develop/test/test_parallel_stable_sort.cpp#L13  
  
To reproduce: change the include order or remove `#include <ciso646>` (requires some tests to be disabled or rewritten).  
  
My env: Visual Studio Community 2019. Version 16.9.3
