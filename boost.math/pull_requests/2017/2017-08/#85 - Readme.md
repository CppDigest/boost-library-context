# #85 Readme [Closed]

> Username: NAThompson  
> Created at: 2017-08-23 22:22:38 UTC  
> Updated at: 2018-10-18 20:59:27 UTC  
> Closed at: 2018-10-18 20:59:20 UTC  
> Url: https://github.com/boostorg/math/pull/85  

This adds a README.rst so I can remember the list of steps to get code into boost/math.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2017-08-24 10:00:52 UTC  
> Url: https://github.com/boostorg/math/pull/85#issuecomment-324591386  

This is a good idea, however, how much does it duplicate: http://www.boost.org/doc/libs/1_64_0/libs/math/doc/html/math_toolkit/special_tut/special_tut_impl.html ?  If possible I'd like to keep all the "how to contribute" stuff in one place.  
  
I think we should also add an extended description of what *is* in the library.  
  
And finally, the existing (and rather old) TODO list here: http://www.boost.org/doc/libs/1_64_0/libs/math/doc/html/math_toolkit/issues.html  needs updating with your suggestions.  
  
Sorry!

---

## Comment 2

> Username: NAThompson  
> Created_at: 2017-08-24 17:17:14 UTC  
> Url: https://github.com/boostorg/math/pull/85#issuecomment-324699626  

I guess we have no clue which page would be read more, a README or the boost.org docs. I personally find the README to be more accessible, but you're right that we need a 'canonical point of authority' for each piece of information.  
  
I could move this information to the qbk files, and simply display a CI badge with the readme, or we could move the TODO list in the qbk to the 'Contributing' section of the README. We might get more contributions and updates to the 'Contributing' and 'TODO' if it's in RST. . .  
  
Do you have a preference?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2017-08-24 17:42:50 UTC  
> Url: https://github.com/boostorg/math/pull/85#issuecomment-324706294  

On 24/08/2017 18:17, Nick wrote:  
>  
> I guess we have no clue which page would be read more, a README or the   
> boost.org docs. I personally find the README to be more accessible,   
> but you're right that we need a 'canonical point of authority' for   
> each piece of information.  
>  
> I could move this information to the qbk files, and simply display a   
> CI badge with the readme, or we could move the TODO list in the qbk to   
> the 'Contributing' section of the README. We might get more   
> contributions and updates to the 'Contributing' and 'TODO' if it's in   
> RST. . .  
>  
> Do you have a preference?  
>  
  
I think I'd prefer the readme to be a quick-ish overview with links to   
the actual docs on the website, otherwise it sort of duplicates what's   
already there.  The only thing I guess is that the readme could be more   
quickly updated, while the online docs refer to the last release.  I   
doubt it makes a huge difference though?  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 4

> Username: jeking3  
> Created_at: 2018-07-24 10:57:54 UTC  
> Url: https://github.com/boostorg/math/pull/85#issuecomment-407366049  

Shouldn't this be named README.md so it shows up at the top level of a github view?  Also suggest adding a LICENSE file - you can copy one from the top level of https://github.com/jeking3/boost-ci to this repository so github recognizes this repository as a BSL-1.0 licensed project.

---

## Comment 5

> Username: NAThompson  
> Created_at: 2018-07-24 12:08:57 UTC  
> Url: https://github.com/boostorg/math/pull/85#issuecomment-407383563  

> Shouldn't this be named README.md so it shows up at the top level of a github view?  
  
github supports both .rst and .md format.

---

## Comment 6

> Username: pabristow  
> Created_at: 2018-07-24 13:09:44 UTC  
> Url: https://github.com/boostorg/math/pull/85#issuecomment-407400468  

From: Nick [mailto:notifications@github.com]  
Sent: 24 July 2018 13:09  
To: boostorg/math  
Cc: Subscribed  
Subject: Re: [boostorg/math] Readme (#85)  
  
Would it also be useful to provide links to BOTH release AND develop docs so that people can see what is coming soon?  
  
(And I’d favour providing mainly links rather trying to duplicate the ‘real’ docs).

---

## Comment 7

> Username: jeking3  
> Created_at: 2018-07-24 23:32:05 UTC  
> Url: https://github.com/boostorg/math/pull/85#issuecomment-407584906  

For me, the most important information in the README is the project status so that sounds reasonable.   If you look at the DateTime, Format, or Uuid projects on github that's the information I like to provide.

---

## Comment 8

> Username: pabristow  
> Created_at: 2018-07-26 08:16:30 UTC  
> Url: https://github.com/boostorg/math/pull/85#issuecomment-408015756  

readme  https://github.com/boostorg/format,  for example, …   Look good to me.  
  
I generally like the more info section,  
  
but would links to the main boost.org site  
  
and the boost general (not developer) lists for questions be good too?  
  
And I’d like to see a direct link to the release docs index.html for the project, and perhaps another link to the develop branch?  
  
“Note that by submitting patches you agree to license your modifications under the Boost Software License, Version 1.0<http://www.boost.org/LICENSE_1_0.txt>.”  is a good addition.  
  
I’m less convinced by the re-iteration of the directories structure as this duplicates the normal github display?  
  
License is good too, if redundant info, but makes it immediately clear.  
  
  
From: James E. King III [mailto:notifications@github.com]  
Sent: 25 July 2018 00:32  
To: boostorg/math  
Cc: Paul A. Bristow; Comment  
Subject: Re: [boostorg/math] Readme (#85)  
  
  
For me, the most important information in the README is the project status so that sounds reasonable. If you look at the DateTime, Format, or Uuid projects on github that's the information I like to provide.  
  
—  
You are receiving this because you commented.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/math/pull/85#issuecomment-407584906>, or mute the thread<https://github.com/notifications/unsubscribe-auth/ABBuAV6T_OdU-9RtDtTfGoVix8SNCA8Vks5uJ651gaJpZM4PAqUo>.

---
