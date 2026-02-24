# #8 Conditionally provide interfaces based on deprecated/removed std::aut… [Merged]

> Username: DanielaE  
> Created at: 2017-05-23 14:24:29 UTC  
> Updated at: 2017-12-31 18:23:45 UTC  
> Merged at: 2017-11-20 19:53:07 UTC  
> Closed at: 2017-11-20 19:53:07 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/8  

…o_ptr and/or std::unique_ptr, and replace C++98 function adapters by inline typedefs.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: pdimov  
> Created_at: 2017-11-05 16:07:30 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/8#issuecomment-341984189  

I considered merging that but I don't like the introduction of `BOOST_PTR_CONTAINER_NO_AUTO_PTR` and have no time now to remove it. :-)

---

## Comment 2

> Username: DanielaE  
> Created_at: 2017-11-07 06:11:49 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/8#issuecomment-342385847  

So, my question is:  
- don't you like the name?  
- do you want the inclusion of the auto_ptr interface to be dependent on BOOST_NO_AUTO_PTR only?  
- do you want the inclusion of the auto_ptr interface to be dependent on BOOST_NO_CXX11_SMART_PTR only?  
  
My idea was to give users both all technically possible interfaces (i.e. stay as compatible as possible) and an opt-out-of-auto_ptr option, too.

---

## Comment 3

> Username: eldiener  
> Created_at: 2017-11-07 10:30:58 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/8#issuecomment-342440811  

The opt-out-of-auto_ptr option is totally unnecessary. If BOOST_NO_CXX11_SMART_PTR is defined, leave the code as is, else use std::unique_ptr in place of std::auto_ptr. That is all you have to do.

---

## Comment 4

> Username: pdimov  
> Created_at: 2017-11-07 11:11:46 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/8#issuecomment-342450860  

Second bullet, @DanielaE. What you have, just with `BOOST_CONTAINER_NO_AUTO_PTR` removed, and `BOOST_NO_AUTO_PTR` used directly in its place.

---

## Comment 5

> Username: DanielaE  
> Created_at: 2017-11-19 16:07:34 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/8#issuecomment-345527998  

There it is, changes applied as advised.  
I'm sorry for the delay, but attending the Meeting C++ conference, a long business trip, life - you know 😄

---

## Comment 6

> Username: pdimov  
> Created_at: 2017-11-19 17:17:11 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/8#issuecomment-345533248  

No problem, we're all volunteers here. :-)

---

## Review 7 [Commented]

> Username: pdimov  
> Created_at: 2017-11-19 17:18:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ptr_container/pull/8#pullrequestreview-77648884  

📁 test/associative_test_data.hpp

```diff
 106 |     std::auto_ptr<T> ap( new T );
 107 |     c.insert( ap );
 108 |+ #else
```

> Username: pdimov  
> Created_at: 2017-11-19 17:18:44 UTC  
> Updated_at: 2017-11-20 18:25:40 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/8#discussion_r151871067  

Since we're providing the `unique_ptr` overloads when `BOOST_NO_CXX11_SMART_PTR` is not defined, we should probably test them under the same condition, instead of only when `BOOST_NO_AUTO_PTR` is set.

> Username: DanielaE  
> Created_at: 2017-11-19 17:35:12 UTC  
> Updated_at: 2017-11-20 18:25:40 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/8#discussion_r151871477  

Right, but that doesn't work out in all cases: in return values you only have either/or exclusively. Have a peek at line 139:  
```  
#ifndef BOOST_NO_AUTO_PTR  
    std::auto_ptr<C> ap2        = c.release();  
#else  
    std::unique_ptr<C> up2      = c.release();  
#endif  
```  
Sigh - this testing stuff will become tedious then...

> Username: pdimov  
> Created_at: 2017-11-19 17:46:35 UTC  
> Updated_at: 2017-11-20 18:25:40 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/8#discussion_r151871822  

Yes, you're right. Perhaps just do it for the cases where it can be done easily, and leave the tedious ones as is?


---

## Comment 8

> Username: pdimov  
> Created_at: 2017-11-19 19:15:03 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/8#issuecomment-345541715  

Are there any other libraries for which `auto_ptr` pull requests are sitting unmerged?

---

## Comment 9

> Username: eldiener  
> Created_at: 2017-11-19 22:56:01 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/8#issuecomment-345557314  

You should not be testing be testing BOOST_NO_AUTO_PTR. You should only be testing BOOST_NO_CXX11_SMART_PTR. When BOOST_NO_CXX11_SMART_PTR is not defined, use unique_ptr, otherwise use auto_ptr. It really is that simple. If both BOOST_NO_AUTO_PTR and BOOST_NO_CXX11_SMART_PTR is defined you have a broken compiler, and nothing will work so you just do not waste your time with such a possibility.  
  
Please explain why you think you have to test BOOST_NO_AUTO_PTR ?

---

## Comment 10

> Username: pdimov  
> Created_at: 2017-11-19 23:18:06 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/8#issuecomment-345558705  

If we just remove the `auto_ptr` overloads when `BOOST_NO_CXX11_SMART_PTR` isn't defined, won't this break existing code using `auto_ptr` under C++11?

---

## Comment 11

> Username: eldiener  
> Created_at: 2017-11-20 00:52:09 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/8#issuecomment-345565426  

My bad. I did not look to see that auto_ptr is part of the public/protected interface. In that case it should be changed to unique_ptr only if BOOST_NO_AUTO_PTR is defined. My apologies. I had changed a number of libraries from auto_ptr to unique_ptr, but none of them used auto_ptr except for private or detail functionality.

---

## Comment 12

> Username: eldiener  
> Created_at: 2017-11-20 01:00:06 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/8#issuecomment-345566074  

I should have said, for the public/protected interfaces, change auto_ptr to unique_ptr only if BOOST_NO_AUTO_PTR is defined and BOOST_NO_CXX11_SMART_PTR is not defined, else drop the auto_ptr interface entirely; although I can not imagine any compiler for which neither auto_ptr nor unique_ptr is supported for a given C++ standard compiler level.

---

## Comment 13

> Username: DanielaE  
> Created_at: 2017-11-20 12:04:26 UTC  
> Updated_at: 2017-11-20 18:39:20 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/8#issuecomment-345676697  

@pdimov:   
> Are there any other libraries for which auto_ptr pull requests are sitting unmerged?  
  
Yes, there are. I have a list of open PRs which I submitted months ago. I will re-check it when I am back from work.  
  
Update: I have open PRs regarding std::auto_ptr with asio, assign, and statechart.

---

## Comment 14

> Username: DanielaE  
> Created_at: 2017-11-20 18:42:32 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/8#issuecomment-345788882  

@pdimov   
I've updated the tests once again ...

---

## Comment 15

> Username: pdimov  
> Created_at: 2017-11-20 19:17:51 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/8#issuecomment-345799077  

This looks good to me.  
  
I'm seeing one odd error on msvc-14.1/cxxstd-17:  
  
```  
compile-c-c++ ..\..\bin.v2\libs\ptr_container\test\ptr_map_adapter.test\msvc-14.1\debug\cxxstd-17-iso\threadapi-win32\threading-multi\ptr_map_adapter.obj  
ptr_map_adapter.cpp  
c:\program files (x86)\windows kits\10\include\10.0.16299.0\shared\rpcndr.h(192): error C2872: 'byte': ambiguous symbol  
c:\program files (x86)\windows kits\10\include\10.0.16299.0\shared\rpcndr.h(191): note: could be 'unsigned char byte'  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Tools\MSVC\14.11.25503\include\cstddef(15): note: or       'std::byte'  
```  
  
This doesn't look like our problem, but it might be possible to work around it (or it could be my config.)

---

## Comment 16

> Username: pdimov  
> Created_at: 2017-11-20 19:25:46 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/8#issuecomment-345801628  

Fixed it, needed to change `<boost/test/included/unit_test.hpp>` to `<boost/test/unit_test.hpp>`. I'm ready to merge if Edward doesn't object.

---

## Comment 17

> Username: eldiener  
> Created_at: 2017-11-20 19:46:43 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/8#issuecomment-345808460  

I do not object since I messed up originally in not understanding that the PR dealt with non-private interfaces. The code looks good and I am sorry I was such an annoyance.

---

## Comment 18

> Username: pdimov  
> Created_at: 2017-11-20 19:57:13 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/8#issuecomment-345811809  

https://github.com/boostorg/asio/pull/51 seems no longer relevant.

---

## Comment 19

> Username: pdimov  
> Created_at: 2017-11-20 19:59:22 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/8#issuecomment-345812491  

Nobody has write access to Statechart. :-)

---

## Comment 20

> Username: pdimov  
> Created_at: 2017-11-20 20:00:37 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/8#issuecomment-345812907  

Assign's Travis fails, will need to figure out why.

---

## Comment 21

> Username: DanielaE  
> Created_at: 2017-11-21 07:01:13 UTC  
> Updated_at: 2017-11-21 07:03:21 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/8#issuecomment-345935154  

Great, Boost.ASIO got some love by Chris 👍   
So it seems like Boost.Locale is the last lib standing. AFAIK Artyom objects changes on behalf of auto_ptr.

---
