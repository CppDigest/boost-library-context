# #29 fix build break on msvc-8.0 [Merged]

> Username: ericniebler  
> Created at: 2015-04-18 18:45:08 UTC  
> Updated at: 2015-04-21 07:31:35 UTC  
> Merged at: 2015-04-19 20:28:52 UTC  
> Closed at: 2015-04-19 20:28:52 UTC  
> Url: https://github.com/boostorg/range/pull/29  

811b271 caused failures on msvc-8.0. This restores order. It also includes a drive-by fix to a bad hack in sub_range.hpp for msvc. I have no idea how this was ever compiling.

---

## Comment 1

> Username: ericniebler  
> Created_at: 2015-04-19 19:05:08 UTC  
> Url: https://github.com/boostorg/range/pull/29#issuecomment-94305540  

@neilgroves I plan to merge this change soon (~24 hrs) unless I hear from you. I want to see this fix in master before C++Now.

---

## Comment 2

> Username: neilgroves  
> Created_at: 2015-04-19 20:27:03 UTC  
> Url: https://github.com/boostorg/range/pull/29#issuecomment-94310632  

Eric,  
  
That's fine with me , please go ahead.  
  
Many thanks for your work.  
  
Regards,  
Neil  
On 19 Apr 2015 20:05, "Eric Niebler" notifications@github.com wrote:  
  
> @neilgroves https://github.com/neilgroves I plan to merge this change  
> soon (~24 hrs) unless I hear from you. I want to see this fix in master  
> before C++Now.  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/range/pull/29#issuecomment-94305540.

---

## Comment 3

> Username: ericniebler  
> Created_at: 2015-04-21 05:44:19 UTC  
> Url: https://github.com/boostorg/range/pull/29#issuecomment-94646495  

The test results on develop look good to me. If there's no objection, I'd like to merge my two recent changes to master.

---

## Comment 4

> Username: neilgroves  
> Created_at: 2015-04-21 07:31:34 UTC  
> Url: https://github.com/boostorg/range/pull/29#issuecomment-94670261  

Hi Eric,  
  
I've merged this to master myself. I like to ensure I understand completely  
the changes that happen on the master branch.  
  
I really appreciate the help. I'm particularly grateful for the MSVC 8  
testing. I was searching for an old Windows VM image! I struggle to test  
the MSVC workarounds well. I must do better and make a Windows VM a  
standard part of my work.  
  
Good luck with CPP now. I wish I could escape from my keyboard sufficiently  
long to attend. I still hope to provide some code to persuade you away from  
projections. I love everything else about the proposal. I perceive  
Projections to be a huge interface design error. I'm 60% convinced I'll  
find out why I am wrong when I write the code to use factory function  
predicates. If we don't have a language where we can avoid that sort of  
combinatorial explosion, there is something very wrong from my perspective.  
The rest of the design is such an awesome leap forward. I'll try and  
persuade you with some code and data!  
  
Thanks,  
Neil  
  
On 21 April 2015 at 06:44, Eric Niebler notifications@github.com wrote:  
  
> The test results on develop look good to me. If there's no objection, I'd  
> like to merge my two recent changes to master.  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/range/pull/29#issuecomment-94646495.

---
