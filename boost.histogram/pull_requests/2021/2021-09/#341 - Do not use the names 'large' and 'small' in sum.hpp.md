# #341 Do not use the names 'large' and 'small' in sum.hpp [Closed]

> Username: emmenlau  
> Created at: 2021-09-29 12:37:59 UTC  
> Updated at: 2021-10-19 13:25:36 UTC  
> Closed at: 2021-09-30 06:57:36 UTC  
> Url: https://github.com/boostorg/histogram/pull/341  

Sadly, we have a problem building against histogram on MSVC with the Visual Studio v2019 compiler and also with the ClangCl 12.0.1 frontend compiler. The problem comes from Microsoft header `C:\Program Files (x86)\Windows Kits\10\include\10.0.19041.0\shared\rpcndr.h` that defines the name `small`:  
```  
C:\Debug\include\boost/histogram/accumulators/sum.hpp(49,46): error: cannot combine with previous 'type-name' declaration specifier  
  sum(const_reference large, const_reference small) noexcept  
  
C:\Program Files (x86)\Windows Kits\10\include\10.0.19041.0\shared\rpcndr.h(190,15): note: expanded from macro 'small'  
#define small char  
```  
  
Basically the build breaks when including `windows.h` in addition to the boost histogram algorithms. We tried the various defines that make `windows.h` lean and mean, but none of this helped.  
  
This PR just renames `small` and `large` in sum.hpp to `small_part` and `large_part`, which I hope are also good names? Of course the real culprit here is the MSVC header, but it would be great to just get this resolved in boost histogram instead of other workarounds that fight the Microsoft defines...

---

## Comment 1

> Username: HDembinski  
> Created_at: 2021-09-29 12:51:55 UTC  
> Updated_at: 2021-09-29 12:56:36 UTC  
> Url: https://github.com/boostorg/histogram/pull/341#issuecomment-930145744  

Thanks for the patch, but I cannot accept it as is, it breaks the API. Let me consider some other options. Note that the problem is really the windows header which violates the C++ standard on how to use the preprocessor.  
  
https://stackoverflow.com/questions/21165891/is-small-a-keyword-in-c

---

## Comment 2

> Username: henryiii  
> Created_at: 2021-09-29 12:57:18 UTC  
> Url: https://github.com/boostorg/histogram/pull/341#issuecomment-930149738  

Could you `#undef` `small`, perhaps? I've had to fight this with `min` and `max`. One trick is writing ` (std::numeric_limits<double>::max)()`, for example..

---

## Comment 3

> Username: HDembinski  
> Created_at: 2021-09-29 13:01:05 UTC  
> Url: https://github.com/boostorg/histogram/pull/341#issuecomment-930152881  

@henryiii Right, that is what I meant by "Let me consider some other options."

---

## Comment 4

> Username: emmenlau  
> Created_at: 2021-09-29 13:06:24 UTC  
> Url: https://github.com/boostorg/histogram/pull/341#issuecomment-930157193  

> Could you `#undef` `small`, perhaps? I've had to fight this with `min` and `max`. One trick is writing ` (std::numeric_limits<double>::max)()`, for example..  
  
We have tried to work around this by undefining `small`, but this piles up to large amounts of nasty hacks. Ideally for windows users one would need to re-define `small` later again, etc...

---

## Comment 5

> Username: emmenlau  
> Created_at: 2021-09-29 13:07:00 UTC  
> Url: https://github.com/boostorg/histogram/pull/341#issuecomment-930157918  

> @henryiii Right, that is what I meant by "Let me consider some other options."  
  
Yes, I very much see your point here! Sorry that I did not consider API stability. Alternatives are very much welcome.

---

## Comment 6

> Username: HDembinski  
> Created_at: 2021-09-29 15:40:23 UTC  
> Url: https://github.com/boostorg/histogram/pull/341#issuecomment-930296518  

As Henry pointed out, you can fix it locally in your local code.  
  
* Import the Boost.Histogram headers before the windows.h header. This will fix the compilation unless you are actually calling `accumulators::sum::small` in your code, which you normally should not have to.  
* If you have to call `accumulators::sum::small` in your code, insert `#undef small` after you inserted all windows headers.  
* In Boost, we generally implement the workaround for the `min` and `max` macros that Windows uses with the workaround that Henry mentioned. I am hesitant to also implement a workaround for the `small` define, because where does it end, but I am going to consider it.

---

## Comment 7

> Username: henryiii  
> Created_at: 2021-09-29 15:48:55 UTC  
> Updated_at: 2021-09-29 16:00:39 UTC  
> Url: https://github.com/boostorg/histogram/pull/341#issuecomment-930303721  

> because where does it end  
  
`windows.h` is a very common header, and is a mess, but should be quite stable, so I'm naively guessing this list won't grow. Though up until now I didn't realize (remember? seems familiar, actually) that `small` was taken. `min` and `max` have always been a headache. (They can never add another `#def` like those!) What about adding `windows.h` to the tests? Just add a conditional include for it in one of the tests? (I don't do that in other packages, so maybe there's a reason for that?)  
  
> piles up to large amounts of nasty hacks  
  
You are talking about Windows...  
  
For now, unless something can be done at a library level, is there anything wrong with:  
  
```cpp  
#undef small  
#include <boost/histogram...>  
#define small char  
```  
  
(or option 1 @HDembinski mentioned above - this is more-or-less option 2) This is not that large or ugly. Well, `#define small char` is ugly, but not large.

---

## Comment 8

> Username: HDembinski  
> Created_at: 2021-09-29 16:11:10 UTC  
> Updated_at: 2021-09-29 16:14:32 UTC  
> Url: https://github.com/boostorg/histogram/pull/341#issuecomment-930322884  

> `windows.h` is a very common header, and is a mess, but should be quite stable  
  
That's why I said I am going to consider it and I have. Unfortunately, I cannot use the trick that we use for `min` and `max` and I could not find a way to temporarily un-define `small` in our header and then re-define it later in general. I can do it only if I assume that `small` is defined to `char`.  
  
I won't add `windows.h` to the tests. If we find a fix then I am going to tests this with the line `#define small char`.

---

## Comment 9

> Username: HDembinski  
> Created_at: 2021-09-30 06:57:36 UTC  
> Url: https://github.com/boostorg/histogram/pull/341#issuecomment-930880195  

@emmenlau I explored different options how we can implement a workaround for `windows.h`, but the error is with them, and it is an easy fix for them, too, they just need to replace `#define small char` with `typedef char small;`. I suggested that you post an issue on a Microsoft bug tracker.  
  
If there was a specific change that we could apply in this library to workaround this issue in the mean-time, I would implement it, but it is not possible to (temporarily) rename a preprocessor macro.  
  
https://stackoverflow.com/questions/34756512/how-to-rename-a-c-preprocessor-macro  
  
Therefore I am closing this now.

---

## Comment 10

> Username: emmenlau  
> Created_at: 2021-09-30 07:03:34 UTC  
> Url: https://github.com/boostorg/histogram/pull/341#issuecomment-930888161  

Hi @HDembinski , I understand and it is fine for us! I do not think that Microsoft will change the headers, otherwise they probably could have worked around the min/max issue years ago. But we can work around this in our code.  
  
Also I think the issue will come up again in the future, so its good to have this discussion here to track the findings!  
  
Happy coding and all the best!

---

## Comment 11

> Username: HDembinski  
> Created_at: 2021-09-30 07:30:32 UTC  
> Updated_at: 2021-09-30 07:31:28 UTC  
> Url: https://github.com/boostorg/histogram/pull/341#issuecomment-930929148  

> Hi @HDembinski , I understand and it is fine for us! I do not think that Microsoft will change the headers, otherwise they probably could have worked around the min/max issue years ago. But we can work around this in our code.  
  
@emmenlau It would still be great if you could reported this to Microsoft, because if no one complains, then nothing is going to change.  
   
> Also I think the issue will come up again in the future, so its good to have this discussion here to track the findings!  
  
I agree and I opened #342 for further discussion.

---

## Comment 12

> Username: emmenlau  
> Created_at: 2021-09-30 08:16:40 UTC  
> Url: https://github.com/boostorg/histogram/pull/341#issuecomment-931004157  

> > Hi @HDembinski , I understand and it is fine for us! I do not think that Microsoft will change the headers, otherwise they probably could have worked around the min/max issue years ago. But we can work around this in our code.  
>   
> @emmenlau It would still be great if you could reported this to Microsoft, because if no one complains, then nothing is going to change.  
  
Yes, I fully agree! I'll report it upstream and will post the link here and in #342.

---

## Comment 13

> Username: henryiii  
> Created_at: 2021-09-30 12:54:54 UTC  
> Updated_at: 2021-09-30 12:58:33 UTC  
> Url: https://github.com/boostorg/histogram/pull/341#issuecomment-931294939  

> it is an easy fix for them  
  
It is absolutely not an easy fix. Changing this would break every library that has an unprotected `#undef small` in it. Windows very specialty is that is is insanely backward compatible; unlike Apple, Microsoft does not break compatibly with abandoned software from 20 years ago. So `min`, `max`, and `small` are going to be `#def`'s forever. Not that adding to the list of complainers isn't bad, go ahead, but it's not going to do anything. By Hyrum's law, windows.h is untouchable.  
  
I'm surprised that `#define WIN32_LEAN_AND_MEAN` or something simliar didn't work; having a pre-define would be a good way to fix this in a backwards compatible way. `#define NOMINMAX` was added for `min`/`max`. A `#define NOSMALL` might be a good outcome!

---

## Comment 14

> Username: HDembinski  
> Created_at: 2021-09-30 14:25:28 UTC  
> Url: https://github.com/boostorg/histogram/pull/341#issuecomment-931372320  

> It is absolutely not an easy fix.  
  
For the libs under their control, they can protect the `#undef small`. For third-party libs, they can provide a compatibility header. There are standard ways to handle this. You should not make excuses for them and defend bad design decisions and unwillingness to improve. Not here, anyway.

---

## Comment 15

> Username: henryiii  
> Created_at: 2021-09-30 16:32:07 UTC  
> Updated_at: 2021-09-30 16:33:07 UTC  
> Url: https://github.com/boostorg/histogram/pull/341#issuecomment-931480878  

I'd hope libs under their control use `char` instead of `small`. :) The problem is that compiling proprietary libraries can't break, to the best of my knowledge, this is a core design choice of Windows. The following code must always work:  
  
```cpp  
#include <windows.h>  
#undef small  
```  
  
That might be, no, will be in old, proprietary software written in the 90's by a company that is defunct, and it still works. They can't break that. They can add a define that undoes bad decisions (something like `WIN32_LEAN_AND_MEAN`, that seems to just exclude some headers) or one for each mistake (a new one, `NOSMALL`?). Windows does not have a depreciation period, or any reasonable way to "undo" mistakes. That's still a very helpful outcome of raising an issue about this!  
  
Not defending this decision, or making excuses for it, but assuming Windows will drop something that will break code isn't going to set the right expectations. Personally, I'd be fine to change it to a typedef, or even just drop the thing altogether; `small` is one character longer than `char`, and I doubt even on a 16 bit system that it was shorter than a `char` (:wink:), and there aren't any of those left anyway (though, admittedly, you can still do 16-bit software in Windows, with a tiny bit of effort). Removing small, min, and max as macros would be amazing! That's something macOS probably would do (they have a deprecation period), but not Windows.

---

## Comment 16

> Username: henryiii  
> Created_at: 2021-09-30 16:36:48 UTC  
> Url: https://github.com/boostorg/histogram/pull/341#issuecomment-931484178  

(Very curious to see the outcome of the issue, though!)

---

## Comment 17

> Username: emmenlau  
> Created_at: 2021-10-01 10:16:49 UTC  
> Url: https://github.com/boostorg/histogram/pull/341#issuecomment-932103493  

So I tried to provide this feedback to Microsoft but its not really easily possible. The issue was already reported i.e. here: https://developercommunity.visualstudio.com/t/winrtWindowsUIInputh-via-rpcndrh/515815?space=8&q=rpcndr.h  
  
However it seems it was rejected because the relevant header belongs to a Windows SDK, not to Visual Studio. I've tried to use the "Windows Feedback Hub" that was claimed to be the right channel for this kind of feedback. But for unknown reasons, I can not type any text in the textbox of Feedback Hub. Furthermore I'm not certain that the Feedback Hub is indeed a channel that is connected to the Windows SDK development. But I can not find a more suitable forum, since https://social.msdn.microsoft.com/Forums/en-US/home?forum=Win10SDKToolsIssues is closed.

---

## Comment 18

> Username: henryiii  
> Created_at: 2021-10-01 15:41:25 UTC  
> Url: https://github.com/boostorg/histogram/pull/341#issuecomment-932339735  

https://social.msdn.microsoft.com/Forums/en-US/home?forum=Win11SDKToolsIssues is not closed.

---

## Comment 19

> Username: henryiii  
> Created_at: 2021-10-19 13:25:36 UTC  
> Url: https://github.com/boostorg/histogram/pull/341#issuecomment-946721679  

This looks familiar: https://www.reddit.com/r/cpp/comments/qavr76/rant_please_dont_create_defines_using_common_words/?%24deep_link=true&correlation_id=13c5af53-7538-4bb5-829c-353b9b3e2b14&post_fullname=t3_qavr76&post_index=3&ref=email_digest&ref_campaign=email_digest&ref_source=email&utm_content=post_title&%243p=e_as&_branch_match_id=739566102989051944&utm_medium=Email%20Amazon%20SES  
  
Specifically: `near`, `far`, `small`, `min`, and `max`.

---
