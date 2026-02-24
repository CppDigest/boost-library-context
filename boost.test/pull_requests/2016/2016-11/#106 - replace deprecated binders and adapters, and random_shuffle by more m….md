# #106 replace deprecated binders and adapters, and random_shuffle by more m… [Merged]

> Username: DanielaE  
> Created at: 2016-11-06 15:03:29 UTC  
> Updated at: 2017-05-23 07:11:57 UTC  
> Merged at: 2017-05-22 21:53:45 UTC  
> Closed at: 2017-05-22 21:53:45 UTC  
> Url: https://github.com/boostorg/test/pull/106  

**replace deprecated binders and adapters, and random_shuffle by more modern equivalents**  
  
Required with latest msvc versions and compiler option /std:c++latest

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2016-11-06 17:39:00 UTC  
> Url: https://github.com/boostorg/test/pull/106#issuecomment-258696656  

Thanks for the patch, however your changes need C++11, which is not allowed unfortunately with our library. We have several locations where we have mixed C++98/C++11(+) code. Do you feel like you can rework your patch such that it is compatible with pre and post C++11?

---

## Comment 2

> Username: DanielaE  
> Created_at: 2016-11-07 06:35:59 UTC  
> Url: https://github.com/boostorg/test/pull/106#issuecomment-258757771  

Raffi, I think that is possible, but I need your advise on two points:  
- do you mind taking a dependency on Boost.Random? This way I can replace std::random features by boost::random ones without littering the code by conditional compiles.  
- on the other hand, I must conditionally compile the binder stuff and the random_shuffle/shuffle- dichotomy

---

## Comment 3

> Username: rogeeff  
> Created_at: 2016-11-07 08:45:54 UTC  
> Url: https://github.com/boostorg/test/pull/106#issuecomment-258777042  

Let's not introduce the dependency on another boost library from the  
implementation if it is possible. Can we just guard these changes with  
ifdef c++11?  
  
On Mon, Nov 7, 2016 at 1:35 AM, Daniela Engert notifications@github.com  
wrote:  
  
> Raffi, I think that is possible, but I need your advise on two points:  
> - do you mind taking a dependency on Boost.Random? This way I can  
>   replace std::random features by boost::random ones without littering the  
>   code by conditional compiles.  
> - on the other hand, I must conditionally compile the binder stuff and  
>   the random_shuffle/shuffle- dichotomy  
>   
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> https://github.com/boostorg/test/pull/106#issuecomment-258757771, or mute  
> the thread  
> https://github.com/notifications/unsubscribe-auth/AGNZKZXXblzKC85myAfwTykF8EL9J-7_ks5q7sbPgaJpZM4Kqk6R  
> .  
  
##   
  
Gennadiy Rozental

---

## Comment 4

> Username: DanielaE  
> Created_at: 2016-11-07 09:28:21 UTC  
> Url: https://github.com/boostorg/test/pull/106#issuecomment-258785979  

Ok, will do when I am back from work this evening. I must look for the appropriate Boost config macro that will guard the changes. Do you have one offhand?

---

## Comment 5

> Username: rogeeff  
> Created_at: 2016-11-07 22:34:59 UTC  
> Url: https://github.com/boostorg/test/pull/106#issuecomment-258985446  

Can you check with boost dev mailing list? Maybe boost.config developers  
can chime in.  
  
On Mon, Nov 7, 2016 at 2:13 PM, Daniela Engert notifications@github.com  
wrote:  
  
> This is the updated patch. But there is a serious drawback and I have no  
> idea how to get around that: there ist no BOOST_ feature test macro which  
> tells me if std::shuffle is available in <algorithm> or not. I tried to  
> infer it from the presence of <random> but this doesn't work f.e. with  
> vc10. both vc12 and vc14 are fine. Unless someone has a good idea how to  
> get around that problem I'm stuck.  
>   
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> https://github.com/boostorg/test/pull/106#issuecomment-258932669, or mute  
> the thread  
> https://github.com/notifications/unsubscribe-auth/AGNZKQO2TUCm_UVPyDbCcscUy2xaGTsYks5q73hVgaJpZM4Kqk6R  
> .  
  
##   
  
Gennadiy Rozental

---

## Comment 6

> Username: raffienficiaud  
> Created_at: 2016-11-08 12:43:22 UTC  
> Url: https://github.com/boostorg/test/pull/106#issuecomment-259126742  

@DanielaE thanks for your work. The `shuffle` issue is currently under discussion on the boost ML. I will rework your patch a bit, but I have to say I will hardly make it for 1.63.

---

## Comment 7

> Username: DanielaE  
> Created_at: 2016-11-08 15:49:29 UTC  
> Url: https://github.com/boostorg/test/pull/106#issuecomment-259173446  

Hi Raffi,  
  
I will try to pick up the latest discussions on the Boost ML. I am far too busy with my day job and real life to follow in real time. Thanks for looking into this and I am looking forward to your modifications. I never expected it to make it for 1.63 because of timing and the current state of affairs w.r.t. `shuffle`, though.

---

## Comment 8

> Username: raffienficiaud  
> Created_at: 2016-11-08 16:30:11 UTC  
> Url: https://github.com/boostorg/test/pull/106#issuecomment-259185823  

@DanielaE I will have a look, no worries, and cool that you do not expect it for 1.63 :)

---

## Comment 9

> Username: raffienficiaud  
> Created_at: 2017-01-30 07:43:20 UTC  
> Url: https://github.com/boostorg/test/pull/106#issuecomment-275997312  

I still did not have the time to setup a new build agent with the latest VS. Will work on that for 1.64

---

## Comment 10

> Username: raffienficiaud  
> Created_at: 2017-03-18 07:34:30 UTC  
> Url: https://github.com/boostorg/test/pull/106#issuecomment-287522872  

Hi there,  
  
This topic is still open. The latest devs. on the boost ML show that the VS2017 release is working as expected. http://www.boost.org/development/tests/develop/developer/test.html : column 14.10.  
  
It would be kind of you if you can test with the VS2017 official release.   
  
Thanks!

---

## Comment 11

> Username: raffienficiaud  
> Created_at: 2017-03-22 07:43:42 UTC  
> Url: https://github.com/boostorg/test/pull/106#issuecomment-288321663  

Thanks @DanielaE for trying out Boost.Test with VS2017.  
Things seem to work fine and this PR does not seem to be needed anymore. Would you mind if I close it?

---

## Comment 12

> Username: MarcelRaad  
> Created_at: 2017-03-22 08:00:45 UTC  
> Url: https://github.com/boostorg/test/pull/106#issuecomment-288324553  

@raffienficiaud It's needed for VS2017 when compiling in C++17 mode (the default is C++14).

---

## Comment 13

> Username: raffienficiaud  
> Created_at: 2017-03-22 08:24:39 UTC  
> Url: https://github.com/boostorg/test/pull/106#issuecomment-288329061  

@MarcelRaad good to know! So let's leave this open then.

---

## Comment 14

> Username: DanielaE  
> Created_at: 2017-03-22 09:01:50 UTC  
> Url: https://github.com/boostorg/test/pull/106#issuecomment-288337130  

@raffienficiaud these changes are still required for compilation with /std:c++latest. I've run the test-suite **with** this flag enabled on my branch [my-1.64.0](https://github.com/DanielaE/boost.test/tree/my-1.64.0) and **without** this flag on the master branch of Boost.Test.

---

## Comment 15

> Username: DanielaE  
> Created_at: 2017-05-01 08:49:53 UTC  
> Url: https://github.com/boostorg/test/pull/106#issuecomment-298306696  

There are Boost configuration macros regarding removed C++98 library features available now. I've reworked my patch to take advantage of it. At present, these macros are defined on msvc 14.0 and 14.1 in C++17 mode only, so far. Therefore the modified code paths trigger only in these cases.  
  
Successfully tested on msvc 9.0, 10.0, 12.0, 14.0 and 14.1

---

## Comment 16

> Username: raffienficiaud  
> Created_at: 2017-05-15 10:31:42 UTC  
> Url: https://github.com/boostorg/test/pull/106#issuecomment-301437828  

Thanks again all of you for your patience, I finally have VS2017 running on my build agents and hopefully understood how to force the C++17 flags with ``b2``.  
Pulled to the branch ``origin/topic/PR106-VS2017-C++17-compatibility``, under testing.   
  
Will post here the result pretty soon.

---

## Comment 17

> Username: raffienficiaud  
> Created_at: 2017-05-23 06:41:59 UTC  
> Url: https://github.com/boostorg/test/pull/106#issuecomment-303304920  

In develop

---

## Comment 18

> Username: DanielaE  
> Created_at: 2017-05-23 07:11:57 UTC  
> Url: https://github.com/boostorg/test/pull/106#issuecomment-303310368  

Thanks, Raffi!

---
