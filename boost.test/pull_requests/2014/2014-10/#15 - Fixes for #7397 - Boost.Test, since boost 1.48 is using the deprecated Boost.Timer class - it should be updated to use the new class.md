# #15 Fixes for #7397 - Boost.Test, since boost 1.48 is using the deprecated Boost.Timer class - it should be updated to use the new class [Merged]

> Username: ja11sop  
> Created at: 2014-10-17 23:55:14 UTC  
> Updated at: 2016-08-13 13:55:31 UTC  
> Merged at: 2015-01-12 13:58:44 UTC  
> Closed at: 2015-01-12 13:58:44 UTC  
> Url: https://github.com/boostorg/test/pull/15  

Pull request for https://svn.boost.org/trac/boost/ticket/7397 allowing boost.test to be used boost.timer. Please see the referenced ticket #7397 for full details.

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2014-12-11 15:38:28 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-66636613  

Hi,  
  
Thanks for working on this. From what I understood of your patch, you are duplicating the boost.timer progress, and linking to the new boost::timer class in order to get rid of the problems.   
  
I kind of like the idea of being independent of other libraries. Right now there is a dependency on boost.timer which triggers a dependency on boost.chrono. I am just wondering the level of time accuracy that we need in boost.test and maybe just use clock functions.   
  
We'll get back to your pull request once we talk about this with Gennadiy.

---

## Comment 2

> Username: rogeeff  
> Created_at: 2014-12-11 15:53:32 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-66639170  

I have in mind to add more time dependent functionality. So if it not a big  
deal (header only dependency), I'd rather keep it implemented somewhere  
else.  
  
Gennadiy  
  
On Thu, Dec 11, 2014 at 10:38 AM, Raffi Enficiaud notifications@github.com  
wrote:  
  
> Hi,  
>   
> Thanks for working on this. From what I understood of your patch, you are  
> duplicating the boost.timer progress, and linking to the new boost::timer  
> class in order to get rid of the problems.  
>   
> I kind of like the idea of being independent of other libraries. Right now  
> there is a dependency on boost.timer which triggers a dependency on  
> boost.chrono. I am just wondering the level of time accuracy that we need  
> in boost.test and maybe just use clock functions.  
>   
> We'll get back to your pull request once we talk about this with Gennadiy.  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/test/pull/15#issuecomment-66636613.  
  
##   
  
Gennadiy Rozental

---

## Comment 3

> Username: ja11sop  
> Created_at: 2014-12-11 16:08:05 UTC  
> Updated_at: 2014-12-20 01:46:42 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-66641682  

To be clear this is not a moving of the old boost time code into test. This is changing boost.test to depend on the new boost.timer library - boost.test is currently broken without this change, that is it is not possible to use boost.test with boost.timer, an arguably common need. At least in my case I like to time my tests using the boost.timer facility.  
  
There was some extraneous code in old boost.timer library that boost.test depends on, namely the displaying of progress. In order to make the migration to the new boost.timer as simple as possible I've moved that extraneous code into boost.test which is now the only library with that dependency. It is the simplest and most sensible approach to handling that case.  
  
This allows boost.test to now work - as before - with the new boost.timer library. I'm all for more improvements to be made to boost.test wrt to timer functionality but this patch is the first step to allowing that.  
  
First it removes the dependency from a deprecated library and allows boost.test to no longer be broken when boost.timer happens to be in the same include tree.  
  
Second, it gets this working for everyone with the old functionality still available for those who really need that (who knows what some people do).  
  
Third and lastly, it creates a solid foundation on which to evolve improved timer functionality should be that be something that people need, or boost.test wants to offer.  
  
In short there is no good reason not to apply this (and the other two related patches). I've been running these in every boost version since the breakage occurred.

---

## Comment 4

> Username: rogeeff  
> Created_at: 2014-12-11 16:49:31 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-66649274  

Sure. It boost.test is the only library which uses progress monitor now-  
let's move it. The rest - actual timing - let's keep dependency on other  
libs  
On Dec 11, 2014 11:08 AM, "ja11sop" notifications@github.com wrote:  
  
> To be clear this is not a moving of the old boost time code into test.  
> This is changing boost.test to depend on the new boost.timer library -  
> boost.test is currently broken without this change, that is it is not  
> possible to use boost.test with boost.timer, an arguably common need. At  
> least in my case I like to time my tests using the boost.timer facility.  
>   
> There was some extraneous code in old boost.timer library that boost.test  
> depends on, namely the displaying of progress. In order to make the  
> migration to the new boost.timer as simple as possible I've moved that  
> extraneous code into boost.test which is now the only library with that  
> dependency. It is the simplest and and most sensible approach to handling  
> that case.  
>   
> This allows boost.test to now work - as before - with the new boost.timer  
> library. I'm all for more improvements to be made to boost.test wrt to  
> timer functionality but this patch is the first step to allowing that.  
>   
> First it removes the dependency from a deprecated library and allows  
> boost.test to no longer be broken when boost.timer happens to be in the  
> same include tree.  
>   
> Second, it gets this working for everyone with the old functionality still  
> available for those who really need that (who knows what some people do).  
>   
> Third and lastly, it creates a solid foundation on which to evolve  
> improved timer functionality should be that be something that people need,  
> or boost.test wants to offer.  
>   
> In short there is no good reason not to apply this (and the other two  
> related patches). I've been running these in every boost version since the  
> breakage occurred.  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/test/pull/15#issuecomment-66641682.

---

## Comment 5

> Username: raffienficiaud  
> Created_at: 2014-12-11 16:53:42 UTC  
> Updated_at: 2014-12-11 16:54:27 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-66650007  

Agreed. So I would say let's get rid of the old API completely. Would you mind changing a bit your pull request to also get rid of the possibility to use the old boost.timer class? (ie. remove the possibility to switch via `BOOST_TEST_USE_DEPRECATED_TIMER`)

---

## Comment 6

> Username: ja11sop  
> Created_at: 2014-12-19 02:40:36 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-67591348  

Only seeing this now - so the reason for the macro was to prevent breaking existing code - if it breaks the macro allows you determine the rate of your migration. I'd recommend leaving the patch as it for now and then after a release with the change in and the deprecation noted it can be fully removed in a subsequent release.

---

## Comment 7

> Username: raffienficiaud  
> Created_at: 2014-12-19 08:16:07 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-67609687  

I understand the purpose of the macro, but as indicated, please remove any reference to the old timer classes.  
  
Regards,  
Raffi

---

## Comment 8

> Username: ja11sop  
> Created_at: 2014-12-19 11:31:28 UTC  
> Updated_at: 2014-12-19 12:23:06 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-67627813  

I guess I wasn't clear. I'm suggesting that removing the ability to switch back (at least temporarily) to the deprecated class risks breaking user code. That's not advisable and with boost.test it is never a good idea to force a break. Use of a macro to allow a grace-period for deprecated features is common practice in boost and the reason I went to the extra effort to provide one here. So while I sympathise with the desire for a clean break I am strongly recommending retaining the macro for those that will need it and then later removing the code entirely after one release. It makes no difference to the functionality of the provided patch but avoids potential pitfalls.

---

## Comment 9

> Username: raffienficiaud  
> Created_at: 2014-12-19 13:29:04 UTC  
> Updated_at: 2014-12-19 13:29:38 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-67637641  

You were clear from the very beginning. The timer facility is not part of the boost.test API. So if ppl are using it, it is at their own risk. This is also some more to document, communicate and maintain, and I would also like to avoid that burden.

---

## Comment 10

> Username: ja11sop  
> Created_at: 2014-12-19 13:48:29 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-67639591  

Ok - I see where we differ in our opinion now. I'd maintain that the timing facility is part of the API of boost test. Specifically many scripts and tools have been written to parse the output from boost.test for analysis, capture and display purposes. Upgrading to the new boost.timer will result in different test output. This is desirable in many cases and all our tools rely on the improved information, but likewise many others will expect the old timer output and those users will be faced with a lot of broken scripts. The macro offers those users a chance to upgrade to a new version of boost without breaking their build systems.   
  
I am more than happy to submit a documentation patch to cover the use of the macro and certainly a change such as this would also require a prominent mention in any release notes if and when a merge to master happens. I'm happy to add that too.  
  
Once this is out there and people have a chance to assess the impact on their build tools that rely on boost test output then I'm all for deprecating and removing entirely. That's not going to be a big deal then and will avoid a lot of noise a sudden (irreversible)  breaking change would likely generate.  
  
As regards people who are somehow relying on deprecated boost.timer for other purposes I completely agree with you - it's at their own risk.  
  
Hopefully you can see the reasoning behind retaining the fallback macro now - I should have been more explicit as to the nature of the potential breakages in the first place.

---

## Comment 11

> Username: rogeeff  
> Created_at: 2014-12-26 10:25:31 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-68134817  

Can you please summarize for me what output we want to make defendant on  
this flag?  
  
Gennadiy  
  
On Fri, Dec 19, 2014 at 8:48 AM, ja11sop notifications@github.com wrote:  
  
> Ok - I see where we differ in our opinion now. I'd maintain that the  
> timing facility is part of the API of boost test. Specifically many scripts  
> and tools have been written to parse the output from boost.test for  
> analysis, capture and display purposes. Upgrading to the new boost.timer  
> will result in different test output. This is desirable in many cases and  
> all our tools rely on the improved information, but likewise many others  
> will expect the old timer output and those users will be faced with a lot  
> of broken scripts. The macro offers those users a chance to upgrade to a  
> new version of boost without breaking their build systems.  
>   
> I am more than happy to submit a documentation patch to cover the use of  
> the macro and certainly a change such as this would also require a  
> prominent mention in any release notes if and when a merge to master  
> happens. I'm happy to add that too.  
>   
> Once this is out there and people have a chance to assess the impact on  
> their build tools that rely on boost test output then I'm all for  
> deprecating and removing entirely. That's not going to be a big deal then  
> and will avoid a lot of noise a sudden (irreversible) breaking change would  
> likely generate.  
>   
> As regards people who are somehow relying on deprecated boost.timer for  
> other purposes I completely agree with you - it's at their own risk.  
>   
> Hopefully you can see the reasoning behind retaining the fallback macro  
> now - I should have been more explicit as to the nature of the potential  
> breakages in the first place.  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/test/pull/15#issuecomment-67639591.  
  
##   
  
Gennadiy Rozental

---

## Comment 12

> Username: ja11sop  
> Created_at: 2014-12-26 12:34:32 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-68139527  

Sure - this affects the "Testing Time" output.  
  
Here are some examples for a test "**case**" called "**test_one**" which executed with the following times: 0.527163615s wall, 0.450000000s user, 0.050000000s system (and cpu time = user+system = 0.500000000s). If we assume a POSIX-like system then the deprecated boost.timer will output the CPU time 0.500000000s.  
  
Here are the outputs we get with normal and xml output, as-is (with patch  applied) and then with the macro defined to retain the old format.  
  
#### Normal Log Output  
  
With the patch applied and no macro defined we get the following output:  
  
```  
Leaving test case "test_one"; testing time: 0.527163615s wall, 0.450000000s user + 0.050000000s system = 0.500000000s CPU (94.8%).  
```  
  
and with the macro defined to retain the output format from the deprecated boost.timer we get.  
  
```  
Leaving test case "test_one"; testing time: 500 ms  
```  
  
#### XML Log Output  
  
With the patch applied and no macro defined we get the following output:  
  
```  
<TestingTime>  
    <WallTime>0.527163615</WallTime>  
    <UserTime>0.450000000</UserTime>  
   <SystemTime>0.050000000</SystemTime>  
</TestingTime>  
```  
  
and with the macro defined to retain the old output we get:  
  
```  
<TestingTime>500000</TestingTime>  
```  
  
Hopefully that is clearer. Basically the macro gives users a chance to defer having to update their output parsing to a new format immediately.  
  
The main issue here being that we don't simply add extra information which can be ignored (as is pull request #16) - we replace current information with new information that requires a different interpretation. Our test tools rely on the new format, but likewise there are many people and plugins that rely on the old format.

---

## Comment 13

> Username: rogeeff  
> Created_at: 2014-12-26 21:23:23 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-68158727  

Realistically I can't imagine people rely on specifics of HRF. As for XML  
can we do something like this for now:  
  
<TestingTime>500000  
    <WallTime>0.527163615</WallTime>  
    <UserTime>0.450000000</UserTime>  
   <SystemTime>0.050000000</SystemTime>  
</TestingTime>  
  
Gennadiy  
  
On Fri, Dec 26, 2014 at 7:34 AM, ja11sop notifications@github.com wrote:  
  
> Sure - this affects the "Testing Time" output.  
>   
> Here are some examples for a test "_case_" called "_test_one_" which  
> executed with the following times: 0.527163615s wall, 0.450000000s user,  
> 0.050000000s system (and cpu time = user+system = 0.500000000s). If we  
> assume a POSIX-like system then the deprecated boost.timer will output the  
> CPU time 0.500000000s.  
>   
> Here are the outputs we get with normal and xml output, as-is (with patch  
> applied) and then with the macro defined to retain the old format.  
> Normal Log Output  
>   
> With the patch applied and no macro defined we get the following output:  
>   
> Leaving test case "test_one"; testing time: 0.527163615s wall, 0.450000000s user + 0.050000000s system = 0.500000000s CPU (94.8%).  
>   
> and with the macro defined to retain the output format from the deprecated  
> boost.timer we get.  
>   
> Leaving test case "test_one"; testing time: 500 ms  
>   
> XML Log Output  
>   
> With the patch applied and no macro defined we get the following output:  
>   
> <TestingTime>  
>     <WallTime>0.527163615</WallTime>  
>     <UserTime>0.450000000</UserTime>  
>    <SystemTime>0.050000000</SystemTime>  
> </TestingTime>  
>   
> and with the macro defined to retain the old output we get:  
>   
> <TestingTime>500000</TestingTime>  
>   
> Hopefully that is clearer. Basically the macro gives users a chance to  
> defer having to update their output parsing to a new format immediately.  
>   
> The main issue here being that we don't simply add extra information which  
> can be ignored (as is pull request #16  
> https://github.com/boostorg/test/pull/16) - we replace current  
> information with new information that requires a different interpretation.  
> Our test tools rely on the new format, but likewise there are many people  
> and plugins that rely on the old format.  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/test/pull/15#issuecomment-68139527.  
  
##   
  
Gennadiy Rozental

---

## Comment 14

> Username: ja11sop  
> Created_at: 2014-12-26 21:57:26 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-68159944  

I think you'd be surprised at the value of the HRF output - I'm really saying that people do rely on the specifics of the format (at least on a line by line basis) for handling test output as it is often easier and simpler to handle than XML and, more importantly, can be handled on the fly with much greater ease - while tests are being carried out. I have built several tools that do exactly that. As a real example my [cuppa](https://github.com/ja11sop/cuppa) build tool provides a special test runner that interprets Boost.Test output as it is generated to show test progress, execution times and so on. I am also aware of other (proprietary) build helpers and interpreters that do something similar.  
  
Your suggestion for the XML output is an interesting one - it may well just work however it unfortunately doesn't help the HRF case much. Also another wrinkle to consider here is that there is more than just a difference in format at stake. One of the other reasons for keeping the format linked to the timer version is to retain the same meaning of the times generated. In Windows machines the original boost timer generates a wall time whereas posix-like platforms generates a CPU time.  
  
The patch as supplied I believe addresses all the issues that are present for a seamless migration for those that don't care about the improvements (it just works) while offers a fallback for those that need the old format and meaning. I still suggest the simplest course of action is simply apply the patch.  
  
Your suggestion for adding CPU time to TestingTime may have merit in its own right (in other words, that just being the new format) since it may allow people and tools that rely having a value for that element to just work - even if the meaning has now changed in the case of Windows platforms.  
  
I suggest then that we want to do one of two things. Apply the patch as-is (it covers all the cases we've talked about). Or, apply the patch but with one small change - add the CPU time (User + System) into the TestingTime element in the new XML output (so still keep the macro). Personally I feel the XML becomes a little subtle at that point but I can see the argument that it might help make migration easier for at least those tools that use the XML output for report generation - if indeed it would support that.

---

## Comment 15

> Username: raffienficiaud  
> Created_at: 2014-12-27 12:18:45 UTC  
> Updated_at: 2014-12-27 12:20:40 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-68176860  

Personally I am still in favor of dropping the deprecated timer API and emulate the old output format with the new timer API.   
Your points in keeping the backward compatibility are justified only by the output format, which can be faked by the new timer API anyway, even if the meaning of the timing would be slightly different.   
Moreover, I do not see any benefit in keeping the backward compatibility as a compilation option: if we really want to be backward compatible, it should be at **runtime** through a _command line_ option. This would also let people do the transition by steps, and enabling the possibility to link against a _unique_ boost.test library containing the two behaviors. Old test module would be ran with this option, and new ones without.  
  
As underlined by Gennadiy, for the XML format we all agree that the proposed solution does the job, with `TestingTime` = `UserTime` + `SystemTime`, but expressed in **milliseconds**. For the HRF format, I suggest adding an option `--deprecated_timer_format` that would output the testing time in **milliseconds** as user+system time. The XML format would be insensitive to this `--deprecated_timer_format` option. The default should be the new boost.timer output format

---

## Comment 16

> Username: rogeeff  
> Created_at: 2014-12-28 00:50:11 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-68194910  

I agree with Raffi.  
1. I think we should use runtime switch. In a lot of cases boost test  
   library exists in a system/company environment in a prebuilt form and we  
   want a single version to cover all the users.  
2. We do not want to rely on old Boost.Timer API. The only thing we need to  
   mimic is old output.  
  
Gennadiy  
  
On Sat, Dec 27, 2014 at 7:18 AM, Raffi Enficiaud notifications@github.com  
wrote:  
  
> Personally I am still in favor of dropping the deprecated timer API and  
> emulate the old output format with the new timer API.  
> Your points in keeping the backward compatibility are justified only by  
> the output format, which can be faked by the new timer API anyway, even if  
> the meaning of the timing would be slightly different.  
> Moreover, I do not see any benefit in keeping the backward compatibility  
> as a compilation option: if we really want to be backward compatible, it  
> should be at _runtime_ through a _command line_ option. This would also  
> let people do the move by steps, and allowing the possibility to link  
> against a _unique_ boost.test library allowing the two behaviors.  
>   
> As underlined by Gennadiy, for the XML format we all agree that the  
> proposed solution does the job, with TestingTime = UserTime + SystemTime,  
> but expressed in _milliseconds_. For the HRF format, I suggest adding an  
> option --deprecated_timer_format that would output the testing time in  
> _milliseconds_ as user+system time. The XML format would be insensitive  
> to this --deprecated_timer_format option.  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/test/pull/15#issuecomment-68176860.  
  
##   
  
Gennadiy Rozental

---

## Comment 17

> Username: ja11sop  
> Created_at: 2014-12-30 00:27:09 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-68318757  

To summarise then the consensus appears to be:  
- we recognise there are valid use-cases that require retention of the deprecated Boost.Timer output format  
- however we would prefer to completely remove any dependency on the deprecated Boost.Timer library - instead emulating the required formatting of the deprecated Boost.Timer  
- because some clients of the library use a pre-built Boost.Test we want that emulation to be runtime, not compile-time configured  
  
I can agree with that and believe it won't be too much trouble to update the patch to reflect that - which I'll start doing.

---

## Comment 18

> Username: raffienficiaud  
> Created_at: 2014-12-30 00:28:00 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-68318807  

Many thanks!

---

## Comment 19

> Username: ja11sop  
> Created_at: 2015-01-05 02:03:24 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-68660528  

Apologies for the delay in this - I've been a little snowed under recently but will hopefully get back to this soon

---

## Comment 20

> Username: raffienficiaud  
> Created_at: 2015-01-05 06:50:39 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-68673907  

Sure, we also were quite busy lately

---

## Comment 21

> Username: ja11sop  
> Created_at: 2015-01-07 18:59:02 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-69071965  

Hi Guys, please review the updated pull request. Here is a summary:  
1. The macro `BOOST_TEST_USE_DEPRECATED_TIMER` has been replaced by a runtime cla `--deprecated_timer_format`. Specifying this will give the old HRF output for the deprecated Boost.Timer  
2. The XML output has been updated. Given the following test times 0.527163615s wall, 0.450000000s user, 0.050000000s system (and cpu time = user+system = 0.500000000s) the original patch would output the following times - _all in nanoseconds_. (My earlier comment regarding the XML output was incorrect - regardless though the suggested change still applies)  
  
```  
<TestingTime>500000000</TestingTime>  
<WallTime>527163615</WallTime>  
<UserTime>450000000</UserTime>  
<SystemTime>050000000</SystemTime>  
```  
  
The updated patch will now output:  
  
```  
<TestingTime>500000</TestingTime>  
<CpuTime>500000000</CpuTime>  
<WallTime>527163615</WallTime>  
<UserTime>450000000</UserTime>  
<SystemTime>50000000</SystemTime>  
```  
  
That is, `<TestingTime>` will remain as it is today, a count in _microseconds_ (the HRF output checks if the value is %1000 and displays ms instead of microseconds whereas the XML output just outputs the raw microsecond count). The new fields will all display their values in _nanoseconds_ per the native output from the new Boost.Timer.  
  
This means that existing parsers or scripts that handle the XML output as before (with the exception that Windows users get a more useful time). HRF users relying on the old format need to either update their scripts or enable the option.

---

## Comment 22

> Username: raffienficiaud  
> Created_at: 2015-01-07 19:54:02 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-69081062  

Hi Jamie,  
  
Thanks for your continued efforts. I am happy with the features you implemented. I will review and test it tomorrow. We noticed we changed a lot of files in between, hopefully the merge went well.  
  
I'll get back to you tomorrow (France is in mourning today).  
  
Raffi

---

## Comment 23

> Username: ja11sop  
> Created_at: 2015-01-07 20:09:17 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-69083570  

No problem, take your time and thanks for the quick reply.

---

## Comment 24

> Username: raffienficiaud  
> Created_at: 2015-01-09 22:12:12 UTC  
> Updated_at: 2015-01-09 22:12:45 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-69407983  

Hi,  
  
I merged your pull request to the branch `tractickets/7397`.  
  
I have the following errors now when I try to build and run the unit test. I think it is in connection with the inclusion of `boost/chrono`:  
  
```  
darwin.compile.c++ ../../../bin.v2/libs/test/test/basic_cstring_test.test/darwin-4.2.1/debug/basic_cstring_test.o  
In file included from basic_cstring_test.cpp:21:  
In file included from ../../../boost/test/unit_test.hpp:18:  
In file included from ../../../boost/test/test_tools.hpp:31:  
In file included from ../../../boost/test/tools/old/impl.hpp:19:  
In file included from ../../../boost/test/unit_test_log.hpp:18:  
In file included from ../../../boost/test/tree/observer.hpp:20:  
In file included from ../../../boost/test/utils/timer.hpp:15:  
In file included from ../../../boost/timer/timer.hpp:14:  
In file included from ../../../boost/chrono/chrono.hpp:13:  
In file included from ../../../boost/chrono/system_clocks.hpp:64:  
In file included from ../../../boost/chrono/detail/system.hpp:12:  
In file included from ../../../boost/system/error_code.hpp:14:  
../../../boost/system/config.hpp:34:3: error: Must not define both BOOST_SYSTEM_DYN_LINK and BOOST_SYSTEM_STATIC_LINK  
# error Must not define both BOOST_SYSTEM_DYN_LINK and BOOST_SYSTEM_STATIC_LINK  
  ^  
1 error generated.  
  
    "g++"  -ftemplate-depth-128 -O0 -fno-inline -Wall -pedantic -g -dynamic -gdwarf-2 -fexceptions -Wno-long-long -fPIC  -DBOOST_ALL_NO_LIB=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_CHRONO_STATIC_LINK=1 -DBOOST_CHRONO_THREAD_DISABLED -DBOOST_SYSTEM_DYN_LINK=1 -DBOOST_SYSTEM_NO_DEPRECATED -DBOOST_SYSTEM_STATIC_LINK=1 -DBOOST_TEST_NO_AUTO_LINK=1 -DBOOST_TIMER_DYN_LINK=1 -DBOOST_TIMER_STATIC_LINK=1  -I"../../.." -c -o "../../../bin.v2/libs/test/test/basic_cstring_test.test/darwin-4.2.1/debug/basic_cstring_test.o" "basic_cstring_test.cpp"  
```  
  
Any idea?  
Thanks,  
Raffi

---

## Comment 25

> Username: ja11sop  
> Created_at: 2015-01-09 22:27:02 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-69410268  

Yes - darwin is a little tricky - basically it looks like Boost.Test either adds the static link defines, or adds the dynamic link all macro. That shouldn't happen. Since previously boost.test did not rely on Boost.Timer and hence Boost.System is might have an override that just worked? I don't have darwin here to try this. What's your b2 invocation line? I might be able to try this here on linux.

---

## Comment 26

> Username: raffienficiaud  
> Created_at: 2015-01-09 22:30:33 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-69410907  

Maybe it is reproducible on Linux, because I think in the jamfile the static lib is explicitly required sometimes. I just run b2 from the `boost/libs/test/test` folder. The command should build and run the necessary.

---

## Comment 27

> Username: ja11sop  
> Created_at: 2015-01-09 22:42:02 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-69412631  

Ah ok, so nothing special with b2 then. I'll give it a go and get back to you. If you prefer we can take this off the ticket and I can email you directly. Either way though I'll post back here after I've tried to reproduce.

---

## Comment 28

> Username: ja11sop  
> Created_at: 2015-01-10 00:35:19 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-69426250  

Sorry for the slow reply - I got side-tracked. I can replicate this so I'lI investigate.

---

## Comment 29

> Username: raffienficiaud  
> Created_at: 2015-01-10 13:58:01 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-69456619  

Would the pull request #29 solve the issue?

---

## Comment 30

> Username: ja11sop  
> Created_at: 2015-01-10 15:49:59 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-69460213  

So I've just arrived here to make the very suggestion in  #29 - It was exactly what I was about to suggest but I see I have been beaten to it, so yes please go ahead.

---

## Comment 31

> Username: ja11sop  
> Created_at: 2015-01-10 18:04:09 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-69465271  

Jürgen's pull request is now applied - I somehow missed that earlier.

---

## Comment 32

> Username: raffienficiaud  
> Created_at: 2015-01-10 20:21:53 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-69470420  

I pulled it into the same branch `tractickets/7397`. Now having some other issues on OSX (apart from some UT that are now failing because of the change of format, which I will fix later). The header variant seems broken.   
  
```  
darwin.compile.c++ ../../../bin.v2/libs/test/test/single_header_test.test/darwin-4.2.1/debug/single_header_test.o  
darwin.link ../../../bin.v2/libs/test/test/single_header_test.test/darwin-4.2.1/debug/single_header_test  
Undefined symbols for architecture x86_64:  
  "boost::timer::format(boost::timer::cpu_times const&, short, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&)", referenced from:  
      boost::unit_test::to_string(boost::timer::cpu_times) in single_header_test.o  
  "boost::timer::cpu_timer::stop()", referenced from:  
      boost::unit_test::framework_impl::visit(boost::unit_test::test_case const&) in single_header_test.o  
  "boost::timer::cpu_timer::start()", referenced from:  
      boost::timer::cpu_timer::cpu_timer() in single_header_test.o  
  "boost::system::system_category()", referenced from:  
      ___cxx_global_var_init2 in single_header_test.o  
  "boost::system::generic_category()", referenced from:  
      ___cxx_global_var_init in single_header_test.o  
      ___cxx_global_var_init1 in single_header_test.o  
  "boost::timer::cpu_timer::elapsed() const", referenced from:  
      boost::unit_test::framework_impl::visit(boost::unit_test::test_case const&) in single_header_test.o  
ld: symbol(s) not found for architecture x86_64  
```

---

## Comment 33

> Username: rogeeff  
> Created_at: 2015-01-10 20:31:55 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-69470766  

Should we CC Jurgen?  
  
On Sat, Jan 10, 2015 at 3:21 PM, Raffi Enficiaud notifications@github.com  
wrote:  
  
> I pulled it into the same branch tractickets/7397. Now having some other  
> issues on OSX (apart from some UT that are now failing because of the  
> change of format, which I will fix later). The header variant seems broken.  
>   
> darwin.compile.c++ ../../../bin.v2/libs/test/test/single_header_test.test/darwin-4.2.1/debug/single_header_test.o  
> darwin.link ../../../bin.v2/libs/test/test/single_header_test.test/darwin-4.2.1/debug/single_header_test  
> Undefined symbols for architecture x86_64:  
>   "boost::timer::format(boost::timer::cpu_times const&, short, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&)", referenced from:  
>       boost::unit_test::to_string(boost::timer::cpu_times) in single_header_test.o  
>   "boost::timer::cpu_timer::stop()", referenced from:  
>       boost::unit_test::framework_impl::visit(boost::unit_test::test_case const&) in single_header_test.o  
>   "boost::timer::cpu_timer::start()", referenced from:  
>       boost::timer::cpu_timer::cpu_timer() in single_header_test.o  
>   "boost::system::system_category()", referenced from:  
>       ___cxx_global_var_init2 in single_header_test.o  
>   "boost::system::generic_category()", referenced from:  
>       ___cxx_global_var_init in single_header_test.o  
>       ___cxx_global_var_init1 in single_header_test.o  
>   "boost::timer::cpu_timer::elapsed() const", referenced from:  
>       boost::unit_test::framework_impl::visit(boost::unit_test::test_case const&) in single_header_test.o  
> ld: symbol(s) not found for architecture x86_64  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/test/pull/15#issuecomment-69470420.  
  
##   
  
Gennadiy Rozental

---

## Comment 34

> Username: raffienficiaud  
> Created_at: 2015-01-10 20:36:43 UTC  
> Updated_at: 2015-01-10 20:36:53 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-69470955  

He is already a participant of this PR.

---

## Comment 35

> Username: jhunold  
> Created_at: 2015-01-10 20:48:08 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-69471336  

And watching the repository :-) Will take a look tomorrow.

---

## Comment 36

> Username: raffienficiaud  
> Created_at: 2015-01-10 21:01:54 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-69471784  

I feel like someone breathing over my shoulder :)

---

## Comment 37

> Username: ja11sop  
> Created_at: 2015-01-10 22:53:31 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-69475551  

Hi Raffi, try out my latest change to add Boost.Timer as a library needed by the `single_header_test`. It resolves the issue you are seeing for me using g++. Jamie

---

## Comment 38

> Username: raffienficiaud  
> Created_at: 2015-01-11 10:59:13 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-69490457  

I solves the problem, but I think the problem lies somewhere else. Anyway, for your fix, I need to solve the UT that is failing now and it should be good.

---

## Comment 39

> Username: ja11sop  
> Created_at: 2015-01-11 14:15:03 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-69495807  

Yes - I think I know the issue with the failing UT - will let you know once I have a working patch

---

## Comment 40

> Username: ja11sop  
> Created_at: 2015-01-11 16:08:09 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-69499528  

The issue with the failing unit test was that the old timer (having a lower accuracy) would typically return a 0 time for tests that are executed immediately. In the case of the HRF output used in the pattern matching test it relies on there being no `; testing time ...` appended to the `Leaving test <test_type> ...` message.  
  
Since the pattern matching isn't actually pattern matching at all but a simple by-character comparison it's not feasible to somehow match against a time pattern. It seemed that the best approach was to allow suppression of the timer output altogether in the case of the failing test. In fact I think that's a generally useful feature for people you want to write simple comparison scripts against the test output.  
  
I therefore added a new runtime parameter (and docs for it) called `--suppress_timer_output`. If this is specified no test time output is provided. I also fixed a copy-paste error for `--deprecated_timer_format`.   
  
What I haven't done, but we could consider, is update the test helper in the Jamfile to allow test arguments to be specified. Currently I just add `--suppress_timer_output` directly. So instead of this:  
  
```  
test-btl-lib ( test-rule : test-name : lib-name ? : pattern_file * : source_files * : extra-libs ? : extra-options ? )  
```  
  
we might instead have this  
  
```  
test-btl-lib ( test-rule : test-name : args* :  lib-name ? : pattern_file * : source_files * : extra-libs ? : extra-options ? )  
```  
  
and then we would only specify the option for the failing test.

---

## Comment 41

> Username: raffienficiaud  
> Created_at: 2015-01-11 20:12:10 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-69509193  

Hi,  
I do not think this option is really useful: having accurate timing in logs and yet doing char to char comparison is just a non-sense and a limitation of the output comparison. I would go for running the tests with the old format instead.   
What do you think?

---

## Comment 42

> Username: ja11sop  
> Created_at: 2015-01-11 20:39:56 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-69510367  

Well - I agree, that would have been my preference. The issue is that a non-zero time may be displayed, regardless of format. The test relies on there being no time displayed. Having said that I'll run it a few times here and see if it will do what we want regardless.

---

## Comment 43

> Username: ja11sop  
> Created_at: 2015-01-11 21:08:52 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-69511550  

Quick update - using the old format is not sufficient - the issue is we still produce a displayed time. It should probably be noted that suppressing the timer output is only to allow the test output comparison to be checked.  
  
I wouldn't consider it to be an option you'd not generally, if ever, want to use, except of course in the case of classic test output comparisons. Then you don't really want a timer causing an unwanted diff. In that sense I think the option is not unreasonable, but I do agree it would be nicer if the output comparison test itself was not quite so brittle,  
  
I suspect I ran into some of these issues when I first put the patch together in the SVN days and it probably influenced my macro based approach. Boost.Test though has moved on quite a lot since then so it is reasonable there will be some issues like this now.  
  
Thinking on this more and looking at the code and intent of `match_pattern()` in [test_tools.ipp](https://github.com/boostorg/test/blob/tractickets/7397/include/boost/test/impl/test_tools.ipp#L574) I think suppressing the time output is possibly the correct approach. That we had no time output before was by luck and not by design. Further it appears that the intent of the matching function is to compare two files for an exact match. 'pattern' being a misnomer in this case.  
  
Either way, I think this needs more thought, and it might be that despite an initial dislike of the approach it may in fact be the best approach.

---

## Comment 44

> Username: ja11sop  
> Created_at: 2015-01-12 12:02:39 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-69560566  

Raffi, it turns out I can make this work with only --deprecated_timer_format by updating `has_time()` to only consider the User and System time in that case. Unless you've decided you like the utility of `--suppress_timer_output` I'll go ahead and update the patch to remove the additional option. Also I think it will make sense to also update the `test-btl-lib` rules to take arguments to make the Jamfile cleaner. Let me know your thoughts. Jamie

---

## Comment 45

> Username: ja11sop  
> Created_at: 2015-01-12 12:24:45 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-69562646  

I've gone ahead and removed the `--suppress_timer_output` option. If we decide we want something like that later it will be easy to re-add it. Give the current pull-request a run. All tests pass as expected for me (now I have a proper modular boost setup) - Jamie

---

## Comment 46

> Username: raffienficiaud  
> Created_at: 2015-01-12 13:32:16 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-69569688  

Good, I'll test that. I was wondering why with the old format the unit tests were failing, but you gave me the answer.

---

## Comment 47

> Username: raffienficiaud  
> Created_at: 2015-01-12 13:53:53 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-69572265  

Works.

---

## Comment 48

> Username: ja11sop  
> Created_at: 2015-01-12 13:58:23 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-69572859  

Excellent!

---

## Comment 49

> Username: raffienficiaud  
> Created_at: 2015-01-12 14:00:06 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-69573075  

And merged, thanks again for working on this.

---

## Comment 50

> Username: ja11sop  
> Created_at: 2015-01-12 14:03:44 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-69573530  

No problem - thanks for taking the time to give it careful consideration. I'd very much appreciate you also looking at my other two pull requests when you have the time. I've double-checked #16 and it is good, but I still need to review and update #17, especially in light of the new `--deprecated_timer_format` option. I'll update that pull request when I've done so.

---

## Comment 51

> Username: rogeeff  
> Created_at: 2015-01-12 20:10:56 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-69636837  

I've commented on #16  
  
On Mon, Jan 12, 2015 at 9:03 AM, ja11sop notifications@github.com wrote:  
  
> No problem - thanks for taking the time to give it careful consideration.  
> I'd very much appreciate you also looking at my other two pull requests  
> when you have the time. I've double-checked #16  
> https://github.com/boostorg/test/pull/16 and it is good, but I still  
> need to review and update #17 https://github.com/boostorg/test/pull/17,  
> especially in light of the new --deprecated_timer_format option. I'll  
> update that pull request when I've done so.  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/test/pull/15#issuecomment-69573530.  
  
##   
  
Gennadiy Rozental

---

## Comment 52

> Username: raffienficiaud  
> Created_at: 2015-01-14 21:18:56 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-69993854  

This need to be reworked as boost.timer is creating a lot of issues in other libraries.

---

## Comment 53

> Username: ja11sop  
> Created_at: 2015-01-14 21:33:39 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-69996252  

Ok, I can imagine that might be the case given the ultimate dependency on system and chrono. If you want to point me in the right direction of some of the issues I'll take a look and work with you to find a solution. What's your general feeling about this as a whole?

---

## Comment 54

> Username: rogeeff  
> Created_at: 2015-01-14 22:39:45 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-70007055  

General feeling is that we should have tested this better and develop  
branch is not a good place for experiments. Plus I have number of style  
issues with these changes I am about to push.  
  
As for the fix, we should support purely header only option. What is the  
easiest route for this is the one we should take.  
  
On Wed, Jan 14, 2015 at 4:33 PM, ja11sop notifications@github.com wrote:  
  
> Ok, I can imagine that might be the case given the ultimate dependency on  
> system and chrono. If you want to point me in the right direction of some  
> of the issues I'll take a look and work with you to find a solution. What's  
> your general feeling about this as a whole?  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/test/pull/15#issuecomment-69996252.  
  
##   
  
Gennadiy Rozental

---

## Comment 55

> Username: rogeeff  
> Created_at: 2015-01-14 22:45:36 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-70007964  

I mean, I am about to push fixes  
  
On Wed, Jan 14, 2015 at 5:39 PM, Gennadiy Rozental rogeeff@gmail.com  
wrote:  
  
> General feeling is that we should have tested this better and develop  
> branch is not a good place for experiments. Plus I have number of style  
> issues with these changes I am about to push.  
>   
> As for the fix, we should support purely header only option. What is the  
> easiest route for this is the one we should take.  
>   
> On Wed, Jan 14, 2015 at 4:33 PM, ja11sop notifications@github.com wrote:  
>   
> > Ok, I can imagine that might be the case given the ultimate dependency on  
> > system and chrono. If you want to point me in the right direction of some  
> > of the issues I'll take a look and work with you to find a solution. What's  
> > your general feeling about this as a whole?  
> >   
> > —  
> > Reply to this email directly or view it on GitHub  
> > https://github.com/boostorg/test/pull/15#issuecomment-69996252.  
>   
> ##   
>   
> Gennadiy Rozental  
  
##   
  
Gennadiy Rozental

---

## Comment 56

> Username: raffienficiaud  
> Created_at: 2015-01-14 22:46:12 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-70008037  

Fixes of? I just reverted the merge.

---

## Comment 57

> Username: rogeeff  
> Created_at: 2015-01-14 22:49:31 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-70008530  

hmm.. ok. We'll have to review this more carefully before we merge it again.  
  
On Wed, Jan 14, 2015 at 5:46 PM, Raffi Enficiaud notifications@github.com  
wrote:  
  
> Fixes of? I just reverted the merge.  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/test/pull/15#issuecomment-70008037.  
  
##   
  
Gennadiy Rozental

---

## Comment 58

> Username: ja11sop  
> Created_at: 2015-01-14 22:56:32 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-70009585  

Apart from stylistic issues I assume the merge is causing failures in other libraries? Or is this primarily a build issue (libraries depend on test, but test now depends on timer, system and chrono) which is certainly a  problem (though I've not seen it in _using_ boost for the past few years). My previous fallback was of course to allow using the original boost.timer. If the build dependencies (and issues) are insurmountable it might be feasible to take a step back and use a small, lightweight header-only equivalent to boost.timer - removing the dependency entirely.

---

## Comment 59

> Username: raffienficiaud  
> Created_at: 2015-01-14 23:02:57 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-70010493  

Yes, in almost everything that is using boost.test as header only, or is using boost.timer old API. I should have taken a deeper look to the boost.timer new vs. old API anyway. I am also reluctant in the fallback macro as we have to maintain two branch of compilation and we do not get rid of the initial problem, which was:  
- having a more accurate timing in boost.test  
- getting rid of a deprecated API  
  
All the changes remain in the branch tracticket/7397, and I propose we put these things on hold for a moment.

---

## Comment 60

> Username: ja11sop  
> Created_at: 2015-01-14 23:06:31 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-70011000  

Agreed. I'll take a look at a lightweight header only option, but yes, putting this on hold for the moment is a good idea.

---

## Comment 61

> Username: bchretien  
> Created_at: 2016-08-10 08:49:24 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-238804446  

Any update on this? And in the meantime, is there a workaround for this issue?

---

## Comment 62

> Username: raffienficiaud  
> Created_at: 2016-08-13 13:55:31 UTC  
> Url: https://github.com/boostorg/test/pull/15#issuecomment-239622029  

Unfortunately no. I involves some changes in the boost.timer library to support header only inclusion.

---
