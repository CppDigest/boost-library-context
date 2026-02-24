# #13 Merge branch 'no-float-convert' of https://github.com/jzmaddock/ratio… [Merged]

> Username: jzmaddock  
> Created at: 2016-12-28 19:27:47 UTC  
> Updated at: 2017-03-26 18:51:18 UTC  
> Merged at: 2016-12-28 19:36:29 UTC  
> Closed at: 2016-12-28 19:36:29 UTC  
> Url: https://github.com/boostorg/rational/pull/13  

…nal into develop  
  
# Please enter a commit message to explain why this merge is necessary,  
# especially if it merges an updated upstream into a topic branch.  
#  
# Lines starting with '#' will be ignored, and an empty message aborts  
# the commit.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2016-12-28 19:29:11 UTC  
> Url: https://github.com/boostorg/rational/pull/13#issuecomment-269527396  

This is PR #4 with changes from develop merged in.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2017-02-26 19:07:24 UTC  
> Url: https://github.com/boostorg/rational/pull/13#issuecomment-282578639  

Can you let me know if you plan to merge this to master?  There are some workarounds in Multiprecision which need removing to ensure the 2 libraries continue to interoperate, and it's inconvenient to have master and develop diverge.  Thanks!

---

## Comment 3

> Username: eldiener  
> Created_at: 2017-02-26 19:47:38 UTC  
> Url: https://github.com/boostorg/rational/pull/13#issuecomment-282581572  

I had already merged this to master.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2017-02-27 09:32:04 UTC  
> Url: https://github.com/boostorg/rational/pull/13#issuecomment-282670138  

> I had already merged this to master.  
>  
  
Doh!  So you have, sorry for the noise, John.

---

## Comment 5

> Username: eldiener  
> Created_at: 2017-03-26 12:20:55 UTC  
> Url: https://github.com/boostorg/rational/pull/13#issuecomment-289279641  

Evidently such constructors as boost::rational<int>(1u,1) are now illegal. Was this intended ?

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2017-03-26 17:07:52 UTC  
> Url: https://github.com/boostorg/rational/pull/13#issuecomment-289298739  

On 26/03/2017 13:20, Edward Diener wrote:  
>  
> Evidently such constructors as boost::rational(1u,1) are now illegal.   
> Was this intended ?  
>  
  
As I mentioned on the mailing list, no not intentional.  
  
The direct patch for that issue is more or less trivial.  However,   
looking at the code and tests I can see a whole bunch of related stuff   
which either isn't being tested and/or which looks suspect. I'll get a   
PR in as a matter of urgency.  
  
John.  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 7

> Username: eldiener  
> Created_at: 2017-03-26 17:47:55 UTC  
> Url: https://github.com/boostorg/rational/pull/13#issuecomment-289301732  

Thanks, John. This is on me also for merging the PR. I naively assumed that the tests, which passed with your fix, covered all the important possibilities, so I blindly trusted the fix to be correct and also because it came from you.

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2017-03-26 18:51:18 UTC  
> Url: https://github.com/boostorg/rational/pull/13#issuecomment-289305899  

On 26/03/2017 18:47, Edward Diener wrote:  
>  
> Thanks, John. This is on me also for merging the PR. I naively assumed   
> that the tests, which passed with your fix, covered all the important   
> possibilities, so I blindly trusted the fix to be correct and also   
> because it came from you.  
>  
  
Trust no one! ;)  
  
Part of the problem is that rational had rather few tests and was very   
poorly specified in terms of what should happen in converting   
constructors.... testing fixes now.  
  
John.  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---
