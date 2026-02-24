# #243 - Undefined symbols for architecture x86_64:   "vtable for boost::unit_test::unit_test_log_t" [Closed]

> Username: nsquangexception  
> Created at: 2018-09-21 16:57:51 UTC  
> Updated at: 2020-05-09 09:51:45 UTC  
> Closed at: 2020-05-09 09:51:45 UTC  
> Url: https://github.com/boostorg/test/issues/243  

I can use many Boost libs, but always failed with boost/test while building with Xcode on MacOS  
MACOSX_DEPLOYMENT_TARGET=10.13  
issue message:  
Undefined symbols for architecture x86_64:  
  "vtable for boost::unit_test::unit_test_log_t", referenced from:  
      boost::unit_test::unit_test_log_t::unit_test_log_t() in main.o  
  NOTE: a missing vtable usually means the first non-inline virtual member function has no definition.  
ld: symbol(s) not found for architecture x86_64

---

## Comment 1

> Username: apolukhin  
> Created at: 2018-09-21 18:05:22 UTC  
> Url: https://github.com/boostorg/test/issues/243#issuecomment-423624053  

Are you using the develop version of Boost, or release?

---

## Comment 2

> Username: developer-mayuan  
> Created at: 2018-11-26 00:11:31 UTC  
> Url: https://github.com/boostorg/test/issues/243#issuecomment-441486750  

Have the same issue on MacOS.

---

## Comment 3

> Username: lav0  
> Created at: 2019-04-13 05:43:33 UTC  
> Url: https://github.com/boostorg/test/issues/243#issuecomment-482778829  

the same problem on MacOS. it must link successfully as it does if target platform switched to iOS

---

## Comment 4

> Username: Lastique  
> Created at: 2019-12-13 13:31:50 UTC  
> Url: https://github.com/boostorg/test/issues/243#issuecomment-565442014  

@mclow Please move this issue to [Boost.Test](https://github.com/boostorg/test).

---

## Comment 5

> Username: raffienficiaud  
> Created at: 2019-12-17 06:25:13 UTC  
> Url: https://github.com/boostorg/test/issues/243#issuecomment-566400672  

Hi, sorry to hear that!  
  
(thanks @Lastique and @mclow for forwarding the ticket.)  
  
I use myself Xcode together with Boost.Test on a regular basis, my feeling is that this is an Xcode configuration issue.   
I will ask some questions in order to understand where the problem comes from.   
  
1. the scenario is you build boost.test with `b2`, then you have a project in Xcode and you try to link to the libraries of boost.test that was generated before. Is that correct?  
1. how is the Xcode project being defined? Is that manually or with a tool like CMake?  
1. what are the compiler defines for the test module?  
1. would someone please share the command line of the linker emitted by Xcode?  
1. would someone please share a minimal example reproducing the issue?

---

## Comment 6

> Username: raffienficiaud  
> Created at: 2019-12-20 06:23:02 UTC  
> Url: https://github.com/boostorg/test/issues/243#issuecomment-567805662  

@nsquangexception any update ?

---

## Comment 7

> Username: raffienficiaud  
> Created at: 2020-05-07 22:57:55 UTC  
> Url: https://github.com/boostorg/test/issues/243#issuecomment-625538325  

@nsquangexception @developer-mayuan Any update welcome.
