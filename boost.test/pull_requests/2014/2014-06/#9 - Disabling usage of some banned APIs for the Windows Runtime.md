# #9 [winrt support] Disabling usage of some banned APIs for the Windows Runtime [Closed]

> Username: stgates  
> Created at: 2014-06-17 17:53:47 UTC  
> Updated at: 2015-04-23 02:15:11 UTC  
> Closed at: 2015-04-22 16:34:27 UTC  
> Url: https://github.com/boostorg/test/pull/9  

Adding a macro for turning on/off for debugger support. Attaching a debugger is disabled if running tests for the Windows Runtime.  
  
Disabled some usage of environment variables, which aren't avaliable, when under the Windows Runtime.  
  
Replaced one usage of CreateFileA, which isn't allow in the Windows Runtime with fopen.

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2014-06-17 18:46:05 UTC  
> Updated_at: 2014-07-09 05:53:46 UTC  
> Url: https://github.com/boostorg/test/pull/9#discussion_r13877802  

Hi,  
Please remove this line.

---

## Comment 2

> Username: raffienficiaud  
> Created_at: 2014-06-17 18:46:24 UTC  
> Updated_at: 2014-07-09 05:53:46 UTC  
> Url: https://github.com/boostorg/test/pull/9#discussion_r13877817  

Please remove this line also.

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2014-06-17 18:46:37 UTC  
> Updated_at: 2014-07-09 05:53:46 UTC  
> Url: https://github.com/boostorg/test/pull/9#discussion_r13877827  

Please remove this line

---

## Comment 4

> Username: raffienficiaud  
> Created_at: 2014-06-17 18:47:34 UTC  
> Url: https://github.com/boostorg/test/pull/9#issuecomment-46348710  

Thanks for the pull request. Would it be possible to remove all the copyright notice please?

---

## Comment 5

> Username: stgates  
> Created_at: 2014-06-17 18:52:26 UTC  
> Url: https://github.com/boostorg/test/pull/9#issuecomment-46349297  

No I don't believe so, otherwise I wouldn't be able to contribute the changes back.  
  
Our legal team has approved the contributions only if they contain the copyright for source code changes. This hasn't been in any issue with several other contributions to other libraries is Boost. Is this going to be a blocker for you? For example see https://github.com/boostorg/system/pull/3 and https://github.com/boostorg/predef/pull/5.  
  
Thanks,  
Steve

---

## Comment 6

> Username: raffienficiaud  
> Created_at: 2014-06-17 18:55:32 UTC  
> Url: https://github.com/boostorg/test/pull/9#issuecomment-46349732  

I do not know as I am not the copyright holder. I should ask the other members and I'll get back to you.  
BTW, is CreateFileA also deprecated?

---

## Comment 7

> Username: stgates  
> Created_at: 2014-06-17 18:58:05 UTC  
> Url: https://github.com/boostorg/test/pull/9#issuecomment-46350051  

Ok let me know, it hasn't been a problem so far. CreateFile isn't deprecated it just isn't an API allowed in Windows phone and store applications. It is only available for Windows desktop applications, but isn't deprecated.  
  
Thanks,  
Steve

---

## Comment 8

> Username: rogeeff  
> Created_at: 2014-06-19 04:01:51 UTC  
> Url: https://github.com/boostorg/test/pull/9#issuecomment-46521509  

I I've never understood 100% what these copyright statement are for.  
Including the importance of the years.  
I think this is fine to add more copyright statements, though it become  
ambiguous: who has copyright for what? Is this copyright for these few  
lines MS added?  
  
On Tue, Jun 17, 2014 at 2:58 PM, stgates notifications@github.com wrote:  
  
> Ok let me know, it hasn't been a problem so far. CreateFile isn't  
> deprecated it just isn't an API allowed in Windows phone and store  
> applications. It is only available for Windows desktop applications, but  
> isn't deprecated.  
>   
> Thanks,  
> Steve  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/test/pull/9#issuecomment-46350051.  
  
##   
  
Gennadiy Rozental

---

## Comment 9

> Username: stgates  
> Created_at: 2014-07-02 22:43:01 UTC  
> Url: https://github.com/boostorg/test/pull/9#issuecomment-47846785  

Yes my understanding is the copyright is only for the changes made. This has been the approach I've followed successfully contributing to other libraries in Boost.  
  
Steve

---

## Comment 10

> Username: stgates  
> Created_at: 2014-07-09 05:56:11 UTC  
> Url: https://github.com/boostorg/test/pull/9#issuecomment-48432750  

Ok after some more discussion with legal at Microsoft they've agreed to let me contribute these back without a Microsoft copyright. They've been removed from all the source files I changed here.

---

## Comment 11

> Username: stgates  
> Created_at: 2014-09-02 17:56:01 UTC  
> Url: https://github.com/boostorg/test/pull/9#issuecomment-54191324  

Ping! This has been pending for some time and I've addressed all the comments made. Is this change likely to be accepted?  
  
Thanks,  
Steve

---

## Comment 12

> Username: raffienficiaud  
> Created_at: 2014-09-02 18:39:40 UTC  
> Url: https://github.com/boostorg/test/pull/9#issuecomment-54197712  

Sorry for the delay.  
I will take care of it tonight or tomorrow. Thank you for having addressed my concerns BTW.  
  
Best,  
Raffi  
  
On 02 Sep 2014, at 19:56, Steve Gates notifications@github.com wrote:  
  
> Ping! This has been pending for some time and I've addressed all the comments made. Is this change likely to be accepted?  
>   
> Thanks,  
> Steve  
>   
> —  
> Reply to this email directly or view it on GitHub.

---

## Comment 13

> Username: stgates  
> Created_at: 2014-09-02 18:40:57 UTC  
> Url: https://github.com/boostorg/test/pull/9#issuecomment-54197896  

Great thanks Raffi!

---

## Comment 14

> Username: raffienficiaud  
> Created_at: 2014-09-04 07:38:09 UTC  
> Url: https://github.com/boostorg/test/pull/9#issuecomment-54422760  

Hi,  
  
Sorry, it takes more time than expected. I am on it.  
On 02 Sep 2014, at 20:40, Steve Gates notifications@github.com wrote:  
  
> Great thanks Raffi!  
>   
> —  
> Reply to this email directly or view it on GitHub.

---

## Comment 15

> Username: raffienficiaud  
> Created_at: 2014-09-04 08:53:51 UTC  
> Updated_at: 2014-09-04 08:54:15 UTC  
> Url: https://github.com/boostorg/test/pull/9#discussion_r17101937  

I think it would be better to add definitions like  
  
```  
#if !defined(UNDER_CE) && !BOOST_PLAT_WINDOWS_RUNTIME  
  #define BOOST_TEST_HAS_GETENV_SUPPORT  
  #define BOOST_TEST_HAS_SETENV_SUPPORT    
#endif   
```  
  
and then to propagate these macros in the code, like in `cpp_main.cpp`

---

## Comment 16

> Username: raffienficiaud  
> Created_at: 2014-09-04 08:55:39 UTC  
> Url: https://github.com/boostorg/test/pull/9#discussion_r17102013  

I think this should be   
  
```  
#if defined(BOOST_TEST_HAS_GETENV_SUPPORT)  
```  
  
to enable the functionality on all platforms except the ones that do not support the environment variables (CE + winRT). What do you think?

---

## Comment 17

> Username: raffienficiaud  
> Created_at: 2014-09-04 08:55:53 UTC  
> Url: https://github.com/boostorg/test/pull/9#discussion_r17102022  

(same remark here)

---

## Comment 18

> Username: raffienficiaud  
> Created_at: 2014-09-04 09:04:30 UTC  
> Updated_at: 2014-09-04 09:06:40 UTC  
> Url: https://github.com/boostorg/test/pull/9#discussion_r17102441  

Should this be   
  
```  
#if !defined(UNDER_CE) && !BOOST_PLAT_WINDOWS_RUNTIME  
```  
  
instead? Could be also replaced by  
  
```  
#if defined(BOOST_TEST_HAS_SETENV_SUPPORT)  
```  
  
What do you think?

---

## Comment 19

> Username: raffienficiaud  
> Created_at: 2014-09-04 09:05:31 UTC  
> Url: https://github.com/boostorg/test/pull/9#discussion_r17102488  

same remark as line 81 here

---

## Comment 20

> Username: raffienficiaud  
> Created_at: 2014-09-04 09:07:57 UTC  
> Url: https://github.com/boostorg/test/pull/9#discussion_r17102595  

Do you mean  
  
```  
#if defined(BOOST_TEST_HAS_GETENV_SUPPORT)  
#define BOOST_RT_PARAM_GETENV getenv  
#endif  
  
#if defined(BOOST_TEST_HAS_SETENV_SUPPORT)  
#define BOOST_RT_PARAM_PUTENV ::boost::BOOST_RT_PARAM_NAMESPACE::putenv_impl  
#endif  
```  
  
?

---

## Comment 21

> Username: raffienficiaud  
> Created_at: 2014-09-04 09:08:22 UTC  
> Url: https://github.com/boostorg/test/pull/9#discussion_r17102619  

same remark as line 121 here

---

## Comment 22

> Username: raffienficiaud  
> Created_at: 2014-09-04 09:11:38 UTC  
> Url: https://github.com/boostorg/test/pull/9#issuecomment-54436917  

Hi Steve,  
  
I putted some comments on your pull request.   
https://github.com/boostorg/test/pull/9/files  
  
Could you please review them?  
  
Thanks,  
Raffi  
  
On 04 Sep 2014, at 09:38, Raffi Enficiaud raffi.enficiaud@free.fr wrote:  
  
> Hi,  
>   
> Sorry, it takes more time than expected. I am on it.  
> On 02 Sep 2014, at 20:40, Steve Gates notifications@github.com wrote:  
>   
> > Great thanks Raffi!  
> >   
> > —  
> > Reply to this email directly or view it on GitHub.

---

## Comment 23

> Username: raffienficiaud  
> Created_at: 2014-09-04 09:14:23 UTC  
> Url: https://github.com/boostorg/test/pull/9#discussion_r17102868  

I think this last line is erroneous, since we want the environment variable support on eg linux or OSX. Could be replaced by   
  
```  
#if defined(BOOST_TEST_HAS_GETENV_SUPPORT) || defined(BOOST_TEST_HAS_SETENV_SUPPORT)  
```  
  
?

---

## Comment 24

> Username: raffienficiaud  
> Created_at: 2014-09-04 09:15:26 UTC  
> Url: https://github.com/boostorg/test/pull/9#discussion_r17102916  

Why are you changing this? Is the memory leak detection available on winRT?

---

## Comment 25

> Username: raffienficiaud  
> Created_at: 2014-09-04 09:16:34 UTC  
> Url: https://github.com/boostorg/test/pull/9#discussion_r17102964  

same remark as for line 74 in file `cpp_main.ipp`

---

## Comment 26

> Username: stgates  
> Created_at: 2014-09-04 17:23:08 UTC  
> Url: https://github.com/boostorg/test/pull/9#issuecomment-54513235  

Yes I'll take a look at your comments and address them. Unfortunately I'm busy the next few days and with CppCon next week, I probably won't get to this until the week after.

---

## Comment 27

> Username: raffienficiaud  
> Created_at: 2014-09-04 18:36:52 UTC  
> Url: https://github.com/boostorg/test/pull/9#issuecomment-54524175  

I can also do some parts of course.  
  
Best,  
Raffi  
  
On 04 Sep 2014, at 19:23, Steve Gates notifications@github.com wrote:  
  
> Yes I'll take a look at your comments and address them. Unfortunately I'm busy the next few days and with CppCon next week, I probably won't get to this until the week after.  
>   
> —  
> Reply to this email directly or view it on GitHub.

---

## Comment 28

> Username: jneidlinger  
> Created_at: 2015-04-08 23:30:31 UTC  
> Updated_at: 2015-04-08 23:30:47 UTC  
> Url: https://github.com/boostorg/test/pull/9#issuecomment-91067274  

@stgates Pinging! Any chance this work could be revived? I would LOVE to be using Boost UTF on my Windows Runtime projects.

---

## Comment 29

> Username: stgates  
> Created_at: 2015-04-22 16:34:27 UTC  
> Url: https://github.com/boostorg/test/pull/9#issuecomment-95257059  

@jneidlinger These changes here were to enable running some of the Boost tests covering the Windows Runtime. I'm not entirely sure how easy it would be to use for testing arbitrary Windows Runtime projects.   
  
More of the Windows APIs are being unbanned and allowed in the Windows Runtime, for example the socket and threading APIs. I'm canceling this pull request, as I think the changes are a bit invasive and going to be difficult to maintain.

---

## Comment 30

> Username: jneidlinger  
> Created_at: 2015-04-22 17:10:11 UTC  
> Url: https://github.com/boostorg/test/pull/9#issuecomment-95267281  

@stgates I was more interested in using the same Boost tests we currently use to test some cross platform native libraries we share between Windows apps and WinRT apps.  
  
Are you thinking enough banned APIs will be opened up to use Boost UTF out of the box (Windows) in the future?  
  
Thanks for the update, I appreciate it.

---

## Comment 31

> Username: stgates  
> Created_at: 2015-04-23 02:15:11 UTC  
> Url: https://github.com/boostorg/test/pull/9#issuecomment-95393256  

@jneidlinger I don't know if that will happened, but I don't have any more time to spend on this pull request. Also without regular testing it would be quite easy to break this work.

---
