# #7 Fix compilation on lastest MSVC [Merged]

> Username: BlowaXD  
> Created at: 2017-09-27 10:42:12 UTC  
> Updated at: 2018-03-07 20:00:53 UTC  
> Merged at: 2017-10-25 18:53:44 UTC  
> Closed at: 2017-10-25 18:53:44 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/7  

std::unary_function has been removed from C++ 17 standard so it does not compile anymore on MSVC  
  
Setup :  
Lastest MSVC  
C++ 2017  
  
Thanks to Mylerius for the help.

---

## Comment 1

> Username: fcacciola  
> Created_at: 2017-10-24 18:44:17 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/7#issuecomment-339091463  

I haven't worked on Boost for over a decade now (this one piece of code is 17 years old :)  
And I don't have write access now, so I cannot merge this pull-request.  
  
@brandon-kohn can you do that?  
  
or alternatively:  
  
@Beman can I get write-acesss? and where do I learn about current procedures so I don't mess things up.  
  
TIA

---

## Comment 2

> Username: pdimov  
> Created_at: 2017-10-25 18:42:35 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/7#issuecomment-339430355  

Welcome back, @fcacciola. Enjoy your write access. :-)

---

## Comment 3

> Username: fcacciola  
> Created_at: 2017-10-25 18:49:44 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/7#issuecomment-339432682  

Thank you Peter!

---

## Comment 4

> Username: japj  
> Created_at: 2017-12-01 16:43:09 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/7#issuecomment-348544772  

Hello,  
  
Is there any information when this will land in master (for a future release?)

---

## Comment 5

> Username: fcacciola  
> Created_at: 2018-02-27 21:25:15 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/7#issuecomment-369031415  

I now have another short slot of free time so I'm looking into this.  
The fix itself has been already proposed and verified, but I don't seem to  
have "merge permissions" or whatever.. or I don't understand the process,  
so I'm still figuring out how to "land this into master" for the upcoming  
release.  
  
On Fri, Dec 1, 2017 at 1:43 PM, Jeroen Janssen <notifications@github.com>  
wrote:  
  
> Hello,  
>  
> Is there any information when this will land in master (for a future  
> release?)  
>  
> —  
> You are receiving this because you modified the open/close state.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/numeric_conversion/pull/7#issuecomment-348544772>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AAjlcH2iS6Ff71Qld3NiEMKMdtVfCRS7ks5s8CydgaJpZM4PlkQO>  
> .  
>  
  
  
  
--   
Fernando Cacciola  
SciSoft Consulting, Founder  
http://www.scisoft-consulting.com

---

## Comment 6

> Username: fcacciola  
> Created_at: 2018-02-27 21:28:02 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/7#issuecomment-369032175  

Maybe I don't have to do anything myself manually? The fix is already in "boostorg:develop".

---

## Comment 7

> Username: MarcelRaad  
> Created_at: 2018-02-28 06:52:40 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/7#issuecomment-369139562  

@fcacciola Thanks! You already merged this into develop some months ago. Now it needs to be merged from develop to master so that it appears in a release.

---

## Comment 8

> Username: pdimov  
> Created_at: 2018-02-28 14:47:26 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/7#issuecomment-369261877  

A deadline for changes to master has passed though so it might be best to ask @danieljames to take a look first.

---

## Comment 9

> Username: danieljames  
> Created_at: 2018-02-28 14:57:39 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/7#issuecomment-369265247  

The deadline was major changes, this isn't a major change, so it's still possible. But I'm not sure about this pull request - it really shouldn't be relying on anything from `functional::detail`. It looks like the use of `std::unary_traits` was removed in ebfded1d7d4a2f773e8233c4df6f96131f79d72e so this might not be needed anyway.

---

## Comment 10

> Username: fcacciola  
> Created_at: 2018-02-28 16:45:12 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/7#issuecomment-369301917  

Hi Daniel,  
  
From the history, initially, Brandon Khon just completely removed the use  
of unary_traits (removed in C++17), but apparently, THAT broke MSVC, which  
is why in the latest and currently merged changeset, that was sort of  
"reintroduced" via boost::functional::detail.  
  
It's been ages since I've look at anything here so I'm not sure anymore.  
  
It seems however that you might be right, and the reintroduction of the  
traits from functional::detail might have not been the right move, and just  
removing these altogether was the right fix.  
  
The MSVC issue says:  
  
 "std::unary_function has been removed from C++ 17 standard so it does not  
compile anymore on MSVC  
  
Setup :  
Lastest MSVC  
C++ 2017  
  
Which seems to imply Brandon's fix was already correct. However, the latest  
change was on top of Brandon's fix, so I take it to mean it didn't really  
work on VC 2017?  
  
I don't really have anything setup for testing boost at all. I can do that,  
but if it takes too long, I will be push aside with work as usual and might  
only be able to get back after the deadline, so, if any of you can re-test  
the previous changeset (the one you pointed) with VC 2017 to see if that  
fix really didn't work, we might get this done a lot faster.  
  
PS: What do you use to read and post to the boost development list? I can  
only read it from google groups and "post" by manually emailing to it. Back  
in the days, I used some usenet service, but I changed houses, computers  
and notes so many times since then that now I can't find anything related  
to it.  
  
  
  
  
  
  
  
  
  
On Wed, Feb 28, 2018 at 11:57 AM, Daniel James <notifications@github.com>  
wrote:  
  
> The deadline was major changes, this isn't a major change, so it's still  
> possible. But I'm not sure about this pull request - it really shouldn't be  
> relying on anything from functional::detail. It looks like the use of  
> std::unary_traits was removed in ebfded1  
> <https://github.com/boostorg/numeric_conversion/commit/ebfded1d7d4a2f773e8233c4df6f96131f79d72e>  
> so this might not be needed anyway.  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/numeric_conversion/pull/7#issuecomment-369265247>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AAjlcL1LNApquAVwY2KLLcOwfRWpKCr1ks5tZWljgaJpZM4PlkQO>  
> .  
>  
  
  
  
--   
Fernando Cacciola  
SciSoft Consulting, Founder  
http://www.scisoft-consulting.com

---

## Comment 11

> Username: danieljames  
> Created_at: 2018-03-02 16:48:53 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/7#issuecomment-369979536  

OK, then it's not clear why this was failing, it'd be helpful to see an error message or how to replicate the error. The original report said it was due to the removal of `std::unary_function` from Visual C++, but that can't be the case after it stopped using that. Maybe the error is because the typedefs from `std::unary_function` are used somewhere? In which case, the consensus was that the best solution is to add the typedefs to the structs rather than relying on inheritance.

---

## Comment 12

> Username: StefanAtev  
> Created_at: 2018-03-05 15:19:24 UTC  
> Updated_at: 2018-03-05 15:20:29 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/7#issuecomment-370452507  

The issue depends on whether C++17 mode is enabled for MSVC; The default for MSVC is /std:c++14, in which case unary_function is available and everything compiles. However, with /std:c++17 or /std:c++latest, MSVC no longer provides unary/binary function. Perhaps the confusion of whether things are removed or not is due to different uses of the /std switch. Right now, users have to choose between using some boost libraries like lexical_cast and special math, or C++17 features. A fix for /std:c++17 mode would be greatly appreciated.

---

## Comment 13

> Username: pdimov  
> Created_at: 2018-03-05 15:25:13 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/7#issuecomment-370454485  

> Maybe the error is because the typedefs from `std::unary_function` are used somewhere?  
  
What makes this even more interesting is that I can see in the diff that the typedefs are already present at least in `trivial_converter_impl`.

---

## Comment 14

> Username: pdimov  
> Created_at: 2018-03-05 15:30:55 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/7#issuecomment-370456467  

> A fix for /std:c++17 mode would be greatly appreciated.  
  
A fix for what specific issue with `/std:c++17`? What do you compile, what errors do you get? The tests on `master` already pass with either 14 or 17.

---

## Comment 15

> Username: pdimov  
> Created_at: 2018-03-05 15:33:14 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/7#issuecomment-370457325  

> However, the latest change was on top of Brandon's fix, so I take it to mean it didn't really work on VC 2017?  
  
Then we need an example that demonstrates why it didn't really work.

---

## Comment 16

> Username: StefanAtev  
> Created_at: 2018-03-05 15:41:58 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/7#issuecomment-370460522  

I must be clear - I am reporting the issue as it appears in 1.65.1; If it is already fixed when 1.66 is out, that would be great. With 1.65.1, simply using ibeta from <boost/math/special_functions/beta.hpp> or just including <boost/lexical_cast.hpp> is enough to break with /std:c++17 (using the latest toolchain supported by boost as of 1.65.1, which is MSVC 14.11). The initial report was not clear whether the /std switch was used, and if so, at what setting. It was also not clear exactly what MSVC version was used - 14.10, 14.11, or 14.12.

---

## Comment 17

> Username: danieljames  
> Created_at: 2018-03-05 15:43:05 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/7#issuecomment-370460940  

1.66.0 was released in December.

---

## Comment 18

> Username: StefanAtev  
> Created_at: 2018-03-05 15:43:50 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/7#issuecomment-370461195  

Sorry - my bad - I was writing from memory, we are using 1.66 and waiting for the April release that will support MSVC 14.12

---

## Comment 19

> Username: pdimov  
> Created_at: 2018-03-05 15:45:33 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/7#issuecomment-370461780  

The tests for 1.66 pass with `/std:c++17` for me. `lexical_cast` too.  
  
```  
commit ebfded1d7d4a2f773e8233c4df6f96131f79d72e (HEAD -> master, tag: boost-1.66.0, origin/master)  
Author: Brandon Kohn <blkohn@hotmail.com>  
Date:   Tue Aug 22 13:39:34 2017 -0400  
  
    Remove the deprecated uses of std::unary_function (again).  
```

---

## Comment 20

> Username: StefanAtev  
> Created_at: 2018-03-05 15:48:56 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/7#issuecomment-370462874  

OK - scratch all of that, I must have coffee. The build I am looking at is using 1.65.1; It did not switch to 1.66 because 1.66 did not add MSVC 14.12 support so it did appear to be an useless upgrade; now that we are turning on /std:c++17, it seems that it does bring in other benefits. I apologize for the confusion - and appreciate the quick response.

---

## Comment 21

> Username: fcacciola  
> Created_at: 2018-03-05 18:23:20 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/7#issuecomment-370514273  

On Mon, Mar 5, 2018 at 12:45 PM, Peter Dimov <notifications@github.com>  
wrote:  
  
> The tests for 1.66 pass with /std:c++17 for me. lexical_cast too.  
>  
> commit ebfded1d7d4a2f773e8233c4df6f96131f79d72e (HEAD -> master, tag: boost-1.66.0, origin/master)  
> Author: Brandon Kohn <blkohn@hotmail.com>  
> Date:   Tue Aug 22 13:39:34 2017 -0400  
>  
>     Remove the deprecated uses of std::unary_function (again).  
>  
>  
  
So, what I have to do is just to revert back to Brandon's fix. Do we agree?  
  
  
--   
Fernando Cacciola  
SciSoft Consulting, Founder  
http://www.scisoft-consulting.com

---

## Comment 22

> Username: danieljames  
> Created_at: 2018-03-05 18:51:52 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/7#issuecomment-370523101  

That's good for me. Thanks.

---

## Comment 23

> Username: fcacciola  
> Created_at: 2018-03-06 18:47:46 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/7#issuecomment-370886247  

Already reverted in "develop"  
  
If I open a new PR, base: master <-  compare: develop  
  
the "Create Pull Request" button is disabled. Seems like I need permissions  
to do that.  
  
ANYWAY  
  
If I look into master directly, Brandon had already merged his own changes  
into it (and his changes are the ones that work as far as we can tell)  
  
The documentation changes (and your own fix there) might be merged to  
master too.  
  
  
  
On Mon, Mar 5, 2018 at 3:51 PM, Daniel James <notifications@github.com>  
wrote:  
  
> That's good for me. Thanks.  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/numeric_conversion/pull/7#issuecomment-370523101>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AAjlcCzhA556ebZOYL-zOAdgxhGbODuSks5tbYlIgaJpZM4PlkQO>  
> .  
>  
  
  
  
--   
Fernando Cacciola  
SciSoft Consulting, Founder  
http://www.scisoft-consulting.com

---

## Comment 24

> Username: danieljames  
> Created_at: 2018-03-06 19:02:52 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/7#issuecomment-370890980  

The 'Create Pull Request' button isn't enabled until you enter a title. Although I'd just use command line git to do the merge.  
  
As far as I can tell, the changes in develop are:  
  
* Add a `README.md` for github.  
* Remove workarounds for Visual C++ 6 and 7.  
* Add a meta/libraries.json file.  
  
None of these are really needed for a release, but you can merge them if you want.

---

## Comment 25

> Username: fcacciola  
> Created_at: 2018-03-07 19:34:30 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/7#issuecomment-371257450  

OK.  
After more carefully reviewing all the changes, I created the Pull Request.  
  
I see that I *can* merge the PR myself, but I don't know if I should or is  
that on the hands of the release manager (you)?  
  
  
  
On Tue, Mar 6, 2018 at 4:04 PM, Daniel James <notifications@github.com>  
wrote:  
  
> The 'Create Pull Request' button isn't enabled until you enter a title.  
> Although I'd just use command line git to do the merge.  
>  
> As far as I can tell, the changes in develop are:  
>  
>    - Add a README.md for github.  
>    - Remove workarounds for Visual C++ 6 and 7.  
>    - Add a meta/libraries.json file.  
>  
> None of these are really needed for a release, but you can merge them if  
> you want.  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/numeric_conversion/pull/7#issuecomment-370890980>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AAjlcBP9_lZVjV5CLaOtMYsteMOOM9GIks5tbt1cgaJpZM4PlkQO>  
> .  
>  
  
  
  
--   
Fernando Cacciola  
SciSoft Consulting, Founder  
http://www.scisoft-consulting.com

---

## Comment 26

> Username: danieljames  
> Created_at: 2018-03-07 19:56:26 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/7#issuecomment-371264169  

You can. but I'll be stopping automatic updates of the super project's master branch later, so if you haven't accepted it, I'll do it so that it's included in the beta.

---

## Comment 27

> Username: fcacciola  
> Created_at: 2018-03-07 20:00:52 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/7#issuecomment-371265535  

OK. In that case, I'll do it so you have one less thing to work on.  
  
Best and thank you for your help.  
  
  
  
On Wed, Mar 7, 2018 at 4:56 PM, Daniel James <notifications@github.com>  
wrote:  
  
> You can. but I'll be stopping automatic updates of the super project's  
> master branch later, so if you haven't accepted it, I'll do it so that it's  
> included in the beta.  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/numeric_conversion/pull/7#issuecomment-371264169>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AAjlcCwa-WHB6te94xT45t_RPT_WETeNks5tcDtqgaJpZM4PlkQO>  
> .  
>  
  
  
  
--   
Fernando Cacciola  
SciSoft Consulting, Founder  
http://www.scisoft-consulting.com

---
