# #85 x3::variant needs check-for-self in its member templates [Closed]

> Username: JamesWidman  
> Created at: 2015-04-29 18:15:45 UTC  
> Updated at: 2015-04-29 19:37:52 UTC  
> Closed at: 2015-04-29 19:37:52 UTC  
> Url: https://github.com/boostorg/spirit/pull/85  

I ran into some compile-time errors when trying to copy an object of a type derived from x3::variant.  
  
The root of the issue seems to be the SFINAE check on the members operator=; namely: when variant's move-assignment operator template is considered during overload resolution, it deduces 'A&' for template parameter T, and then reference-collapsing transforms 'variant& &&' into 'variant&', and thus the template ends up being a better choice than the implicitly-declared copy-assignment operator. This leads to a nice tall error message.  
  
It seems like we just need a test for "self" as part of the SFINAE check on that operator template---which we used to have, but it appears to have been commented out at some point (for reasons not immediately clear to me).  
  
In this commit, four of the member templates now use the same compact SFINAE check in the same way (as a default template argument).  It seems to do the right thing for me.

---

## Comment 1

> Username: K-ballo  
> Created_at: 2015-04-29 18:51:57 UTC  
> Url: https://github.com/boostorg/spirit/pull/85#issuecomment-97538542  

I think you are targeting the wrong branch, I doubt this change would touch 123 files. PRs should target the `develop` branch.

---

## Comment 2

> Username: JamesWidman  
> Created_at: 2015-04-29 19:18:20 UTC  
> Url: https://github.com/boostorg/spirit/pull/85#issuecomment-97549139  

Yes... I'm not sure how that happened; I really only touched one file, and there's only one commit.  
  
(it's my first pull request ever; apologies.)

---

## Comment 3

> Username: JamesWidman  
> Created_at: 2015-04-29 19:24:11 UTC  
> Url: https://github.com/boostorg/spirit/pull/85#issuecomment-97550731  

Ah: I see.  I initially had master checked out locally, then switched to develop.  So the next time I pushed, I guess github thought that I wanted to push my develop into master...?  But I really just wanted to push a new commit onto the existing remote "develop" without touching master at all.

---

## Comment 4

> Username: teajay-fr  
> Created_at: 2015-04-29 19:26:04 UTC  
> Url: https://github.com/boostorg/spirit/pull/85#issuecomment-97551372  

You can do that with GitHub, you have to edit the target branch in   
GitHub when you configure your pull request.  
I had that problem too a couple of times.  
  
Regards,  
  
Thomas Bernard  
  
Le 29/04/2015 21:24, JamesWidman a écrit :  
  
> Ah: I see. I initially had master checked out locally, then switched   
> to develop. So the next time I pushed, I guess github thought that I   
> wanted to push my develop into master...? But I really just wanted to   
> push a new commit onto the existing remote "develop" without touching   
> master at all.  
>   
> —  
> Reply to this email directly or view it on GitHub   
> https://github.com/boostorg/spirit/pull/85#issuecomment-97550731.

---

## Comment 5

> Username: JamesWidman  
> Created_at: 2015-04-29 19:37:52 UTC  
> Url: https://github.com/boostorg/spirit/pull/85#issuecomment-97556108  

Cool; thanks!  
  
I'll close this one and open a new one.

---
