# #1582 - unreachable code in asio/buffer.hpp with optimizations [Closed]

> Username: vinniefalco  
> Created at: 2019-04-20 15:00:52 UTC  
> Updated at: 2024-09-15 16:55:54 UTC  
> Closed at: 2020-08-29 09:39:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1582  

MSVC generates this warning:  
  
> 1>c:\users\vinnie\src\boost\boost\asio\buffer.hpp(244): warning C4702: unreachable code  
  
We need to first determine if it is a beast problem.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2019-05-20 15:36:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-494039762  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 2

> Username: Gibson85  
> Created at: 2020-01-07 08:31:40 UTC  
> Updated at: 2020-01-07 08:32:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-571487522  

Same problem here:  
  
```  
D:\Sources\boost\boost\asio\buffer.hpp(244): error C2220: the following warning is treated as an error  
D:\Sources\boost\boost\asio\buffer.hpp(244): error C2220: the following warning is treated as an error  
D:\Sources\boost\boost\asio\buffer.hpp(244): warning C4702: unreachable code  
D:\Sources\boost\boost\asio\buffer.hpp(244): warning C4702: unreachable code  
D:\Sources\boost\boost\asio\buffer.hpp(244): warning C4702: unreachable code  
D:\Sources\boost\boost\asio\buffer.hpp(244): warning C4702: unreachable code  
D:\Sources\boost\boost\asio\buffer.hpp(244): warning C4702: unreachable code  
D:\Sources\boost\boost\asio\buffer.hpp(244): warning C4702: unreachable code  
D:\Sources\boost\boost\asio\buffer.hpp(244): warning C4702: unreachable code  
LINK : fatal error LNK1257: code generation failed  
```

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-01-07 09:55:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-571516750  

@Gibson85 Which version of boost are you running?

---

## Comment 4

> Username: Gibson85  
> Created at: 2020-01-08 09:39:46 UTC  
> Updated at: 2020-01-08 09:40:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-571968399  

I am using version 1.72 (C++17)

---

## Comment 5

> Username: madmongo1  
> Created at: 2020-01-08 09:49:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-571972156  

@Gibson85   
  
Can you quickly check that line to be sure I'm looking the correct code?  
  
My source file is:  
  
```  
  /// Construct a non-modifiable buffer from a modifiable one.  
  const_buffer(const mutable_buffer& b) BOOST_ASIO_NOEXCEPT  
    : data_(b.data()),        <<<--- LINE 244  
      size_(b.size())  
#if defined(BOOST_ASIO_ENABLE_BUFFER_DEBUGGING)  
      , debug_check_(b.get_debug_check())  
#endif // BOOST_ASIO_ENABLE_BUFFER_DEBUGGING  
  {  
  }  
```  
  
Does that match yours?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2020-01-08 12:56:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-572035928  

That's consistent with what I see.

---

## Comment 7

> Username: Gibson85  
> Created at: 2020-01-11 16:28:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-573331468  

Yes, that's the line.

---

## Comment 8

> Username: Gibson85  
> Created at: 2020-01-30 12:53:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-580238840  

The problem is most probably caused by "whole program optimization". Linking e.g. a static lib (using boost internally) against a program causes this warning for me.

---

## Comment 9

> Username: madmongo1  
> Created at: 2020-01-30 14:03:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-580267722  

Thanks for letting us know.   
  
If course this would place the problem firmly outside the domain of beast.  
  
Would you be happy to close the issue?

---

## Comment 10

> Username: vinniefalco  
> Created at: 2020-01-30 14:13:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-580271811  

We have to do something about it, even if it is just to make a minimally reproducible test program that we can show Chris and get it fixed.

---

## Comment 11

> Username: maddinat0r  
> Created at: 2020-02-23 17:51:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-590096364  

Disabling "Whole Program Optimization" did not fix the warning for me, I had to completely disable optimizations with `/Od`.  
Disabling inline function expansion (`/Ob0`) moves the warning from asio to beast:  
```  
C:\libs\boost_1_72_0\boost\beast\core\impl\buffers_cat.hpp(198): warning C4702: unreachable code  
```  
```cpp  
    struct dereference  
    {  
        const_iterator const& self;  
  
        reference  
        operator()(mp11::mp_size_t<0>)  
        {  
            BOOST_BEAST_LOGIC_ERROR_RETURN({},  
                "Dereferencing a default-constructed iterator");  
        }  
  
        template<class I>  
        reference operator()(I)  
        {  
            return *self.it_.template get<I::value>(); // <--- line 198  
        }  
    };  
```  
Using Boost 1.72.0.

---

## Comment 12

> Username: vinniefalco  
> Created at: 2020-02-23 19:33:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-590107075  

Thanks for narrowing this down. We need to figure out how to modify the beast code to make this warning stop.

---

## Comment 13

> Username: laudrup  
> Created at: 2020-03-15 15:04:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-599222089  

For me at least, this also causes a compiler crash (internal compiler error) using MSVC 2017. Although that might not related to the potential bug in boost/asio, it definitely makes it slightly more serious since it's not possible to simply disable the warning.  
  
I've currently worked around it by disabling optimizations for the specific translation unit, but although I do not care too much about performance in my use case, it seems quite wrong, especially considering this is code intended for release.  
  
Is there any other workaround (eg. using another buffer type somehow) or any update on a fix for this?  
  
Thanks.

---

## Comment 14

> Username: vinniefalco  
> Created at: 2020-03-15 15:21:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-599224107  

> Is there any other workaround (eg. using another buffer type somehow) or any update on a fix for this?  
  
No update, no workaround - and it is annoying the shit out of me. If we could put together a small reproducible `main` function that exhibits the problem, then it might be tractable.

---

## Comment 15

> Username: madmongo1  
> Created at: 2020-03-15 19:20:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-599252713  

@laudrup if you could share a complete 1-function program that exhibits the crash I'll take a look.

---

## Comment 16

> Username: laudrup  
> Created at: 2020-03-15 21:14:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-599265542  

@madmongo1   
  
To be honest, I've only experienced the ICE problem in our build pipeline and haven't been able to reproduce the crash on my development machine, but I assume the actual issue is the "unreachable code" warning.  
  
The warning issue can be reproduced by simply compiling the http_client_async example in release mode using either MSVC 2017 or 2019 (haven't tried with earlier versions) using boost 1.72. Not sure if that can be boiled down more than that.  
  
Not really sure how much more I can help since I have limited knowledge of boost asio/beast internals, but of course I'll be happy to provide any help I can.

---

## Comment 17

> Username: korzhp  
> Created at: 2020-04-15 10:47:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-613965530  

@madmongo1 https://developercommunity.visualstudio.com/content/problem/880290/internal-compiler-error-with-msvc-2017-15918-boost.html

---

## Comment 18

> Username: laudrup  
> Created at: 2020-06-10 08:45:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-641849194  

It seems like this has been fixed in later versions of MSVC 2019 from around version 16.6.  
  
To be honest I haven't tested exactly which version fixes the issue (switching compiler version on Windows is a bit of a hassle), but I can compile the http_client_async example just fine with version 16.6.1 at least.  
  
Not sure if any actions should be taken, but I wanted to share that information at least.

---

## Comment 19

> Username: madmongo1  
> Created at: 2020-06-10 09:29:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-641877349  

@laudrup Thanks for letting us know. To be honest, I'm at a loss as to how we should proceed with this, if at all.

---

## Comment 20

> Username: maddinat0r  
> Created at: 2020-06-10 20:17:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-642234732  

Just tried it with VS 2019 version 16.6.0 and 16.6.2, and I'm still getting the same error (tried Boost 1.72.0 and 1.73.0) while building my project. I've also tried compiling the examples (Boost 1.73.0, MSVC v16.6.2, x86 and x64), without much luck either.

---

## Comment 21

> Username: laudrup  
> Created at: 2020-06-11 07:56:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-642479547  

@maddinat0r My bad. I had forgotten that it is only an issue in release builds (ie. with optimizations enabled).  
  
Sorry about the confusion. This is still an issue :-(

---

## Comment 22

> Username: laudrup  
> Created at: 2020-06-11 08:12:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-642487503  

I do seem to be able to suppress the warning (ie. -wd4702) and have the code build and run without any issues though, so the ICE is at least no longer an issue (for me at least).

---

## Comment 23

> Username: maddinat0r  
> Created at: 2020-06-16 17:47:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-644913486  

I did some digging and I think I found the culprit of those warnings:  
https://github.com/boostorg/beast/blob/6f57e5934c1f307e4510ee11dd09594526a3f5d0/include/boost/beast/core/impl/buffers_cat.hpp#L55  
Changing that macro to `return v` for example gets rid of those warnings.  
I think it's safe to say that ignoring that warning is a viable solution, as the compiler is warning about something we intentionally want to happen.

---

## Comment 24

> Username: vinniefalco  
> Created at: 2020-06-16 18:36:42 UTC  
> Updated at: 2020-06-16 18:37:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-644942473  

Beautiful! So, I think a good fix would be:  
```  
#ifdef _MSC_VER  
# define BOOST_BEAST_UNREACHABLE_RETURN(v) return v  
#else  
# define BOOST_BEAST_UNREACHABLE_RETURN(v) __assume(false)  
#endif  
```

---

## Comment 25

> Username: TheStormN  
> Created at: 2020-06-18 16:03:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-646125084  

> I did some digging and I think I found the culprit of those warnings:  
> https://github.com/boostorg/beast/blob/6f57e5934c1f307e4510ee11dd09594526a3f5d0/include/boost/beast/core/impl/buffers_cat.hpp#L55  
>   
>   
> Changing that macro to `return v` for example gets rid of those warnings.  
> I think it's safe to say that ignoring that warning is a viable solution, as the compiler is warning about something we intentionally want to happen.  
  
This does work. Although just ignoring the warning is not an option at all for codebases that are threating warnings as errors.

---

## Comment 26

> Username: madmongo1  
> Created at: 2020-06-18 16:34:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-646150061  

There are 2 uses of this macro that I could see:  
  
Here's one:  
```  
        net::mutable_buffer  
        operator*() const  
        {  
            BOOST_BEAST_LOGIC_ERROR_RETURN({},  
                "Dereferencing a one-past-the-end iterator");  
        }  
```  
  
It seems to me that the use of __assume(false) is an un-necessary optimisation, since in a well-defined program the code will never be called in any case.  
  
__assume(false) is a useful optimisation when (for example) one knows that a certain case will not be taken in a switch statement that is executed often.   
  
In this case the code path will be taken exactly once in a program's life, followed by a break into the debugger.

---

## Comment 27

> Username: TheStormN  
> Created at: 2020-06-18 18:28:27 UTC  
> Updated at: 2020-06-18 18:28:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-646233621  

@madmongo1 Yeah, that's true. Btw you can see similar issue here: https://stackoverflow.com/questions/5512560/is-there-a-workaround-for-this-c4702-link-time-warning  
  
It's the very same problem. The person there is trying to catch an invalid usage of template operator() and it is getting that error.  
  
Anyway, I guess that you can just remove `__assume(false)` as proposed to get the compiler happy. :)

---

## Comment 28

> Username: madmongo1  
> Created at: 2020-06-18 19:46:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-646270195  

Yup, I'll go ahead and do that in the morning.  
  
  
On Thu, 18 Jun 2020 at 20:28, Nikolay Baklicharov <notifications@github.com>  
wrote:  
  
> @madmongo1 <https://github.com/madmongo1> Yeah, that's true. Btw you can  
> see similar issue here:  
> https://stackoverflow.com/questions/5512560/is-there-a-workaround-for-this-c4702-link-time-warning  
>  
> It's the very same problem. The person there is trying to catch an invalid  
> usage of template operator() and it is getting that error.  
>  
> Anyway, I guess that you can just remove __assume(false) as proposed to  
> get the compiler happy. :)  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/1582#issuecomment-646233621>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABHOZSISSLKTUQBGUSFNBATRXJMFVANCNFSM4HHJ2NLQ>  
> .  
>  
  
  
--   
Richard Hodges  
hodges.r@gmail.com  
office: +442032898513  
home: +376841522  
mobile: +376380212

---

## Comment 29

> Username: TheStormN  
> Created at: 2020-07-03 11:31:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-653502512  

@madmongo1 Just a remainder in case you forgot to change that. :)

---

## Comment 30

> Username: madmongo1  
> Created at: 2020-07-03 14:41:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-653579262  

Thanks :) The release has been pretty hectic.

---

## Comment 31

> Username: TheStormN  
> Created at: 2020-07-23 08:35:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-662885872  

@madmongo1 Sorry but I still don't see changes in that regard. Forgot again? :)

---

## Comment 32

> Username: madmongo1  
> Created at: 2020-07-23 09:44:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-662914883  

Not forgotten. Just dealing with all the other stuff :)

---

## Comment 33

> Username: madmongo1  
> Created at: 2020-07-23 11:35:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-662956834  

Hmm. doesn't seem to cure it for me. What have I missed?

---

## Comment 34

> Username: TheStormN  
> Created at: 2020-07-23 11:38:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-662957854  

Don't know, it does fix it for me when I try it...

---

## Comment 35

> Username: vm2mv  
> Created at: 2022-12-19 10:51:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-1357452971  

The problem is back.  
boost 1.81  
Visual Studio 2022 17.4.3  
  
> Disabling inline function expansion (/Ob0) moves the warning from asio to beast:  
  
`boost\beast\core\impl\buffers_cat.hpp(206): warning C4702: unreachable code  
`  
  
```  
        template<class I>  
        reference operator()(I)  
        {  
->          return *self.it_.template get<I::value>();  
        }  
```

---

## Comment 36

> Username: Kaaserne  
> Created at: 2024-09-15 14:44:36 UTC  
> Updated at: 2024-09-15 14:52:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-2351622961  

The problem is back once more (I think) with boost 1.84.  
`BOOST_LIB_VERSOIN "1_84"`  
  
```  
[build] sandbox\build-debug\vcpkg_installed\x64-windows\include\boost\beast\core\impl\buffers_cat.hpp(186) : error C2220: the following warning is treated as an error  
[build] sandbox\build-debug\vcpkg_installed\x64-windows\include\boost\beast\core\impl\buffers_cat.hpp(186) : warning C4702: unreachable code  
```  
  
```cpp  
// buffers_cat.hpp  
template<class I>  
reference operator()(I)  
{  
    return *self.it_.template get<I::value>();  
}  
```  
Compiling with `/W4 /Wx`

---

## Comment 37

> Username: ashtum  
> Created at: 2024-09-15 16:03:40 UTC  
> Updated at: 2024-09-15 16:04:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-2351654116  

This is addressed in https://github.com/boostorg/beast/pull/2803. You need to upgrade to Boost 1.85.

---

## Comment 38

> Username: Kaaserne  
> Created at: 2024-09-15 16:55:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1582#issuecomment-2351676615  

Ah mb
