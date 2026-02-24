# #17 Suspend tests on features not currently available. [Merged]

> Username: fletchjp  
> Created at: 2015-04-30 13:03:25 UTC  
> Updated at: 2017-05-27 05:05:32 UTC  
> Merged at: 2015-04-30 14:18:58 UTC  
> Closed at: 2015-04-30 14:18:58 UTC  
> Url: https://github.com/boostorg/phoenix/pull/17  

I suggest running this small PR.  It will edit test/Jamfile to take out of action 5 tests which cannot run at the moment as a file which they rely on (phoenix/function/lazy_prelude.hpp) was reverted to an earlier version.  This action does not remove the tests themselves, which can be reactivated when lazy_prelude.hpp is restored which I will bring forward a bit later.  The tests were passing on all or most compilers.  My plan is also to update documentation to correspond with the new features.

---

## Comment 1

> Username: djowel  
> Created_at: 2015-04-30 13:20:59 UTC  
> Url: https://github.com/boostorg/phoenix/pull/17#issuecomment-97778698  

what are these and why are they in master but not in develop?

---

## Comment 2

> Username: fletchjp  
> Created_at: 2015-04-30 13:40:16 UTC  
> Url: https://github.com/boostorg/phoenix/pull/17#issuecomment-97794487  

Joel  
  
They are on develop and not master.  I set the PR to go to develop.  
  
These tests have never been on master.  I was using develop to develop things, while master was closed for 1.58.0.  
  
John  
  
---  
  
From: Joel de Guzman [notifications@github.com]  
Sent: 30 April 2015 14:21  
To: boostorg/phoenix  
Cc: Fletcher, John P  
Subject: Re: [phoenix] Suspend tests on features not currently available. (#17)  
  
what are these and why are they in master but not in develop?  
  
�  
Reply to this email directly or view it on GitHubhttps://github.com/boostorg/phoenix/pull/17#issuecomment-97778698.

---

## Comment 3

> Username: fletchjp  
> Created_at: 2015-04-30 13:58:13 UTC  
> Url: https://github.com/boostorg/phoenix/pull/17#issuecomment-97803627  

Just to be clear, this PR is set to merge to develop and not master.  It will clear tests currently failing on develop pending further action later.

---
