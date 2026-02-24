# #50 fusion_adapters : auto adaptation working also in MSVC [Merged]

> Username: daminetreg  
> Created at: 2015-02-02 07:57:27 UTC  
> Updated at: 2015-02-09 13:32:26 UTC  
> Merged at: 2015-02-09 09:57:25 UTC  
> Closed at: 2015-02-09 09:57:25 UTC  
> Url: https://github.com/boostorg/fusion/pull/50  

Hi @djowel and @K-ballo,  
  
It's been a long time I promised to do the job of fixing the issues with MSVC for the fusion_adapters. And finally it's all in this pull-request. The changes are very small, that was due to missing typename keywords, as BOOST_TYPEOF uses different tricks to detect the type following if typeof is enabled or not.  
  
This are my changes to support MSVC correctly and the develop status as of today merged in the fusion_adapters branch. So it is a fast-forward merge, If you like to put this into develop.  
  
I've got also the new test-case "test that adapted members can actually be expressions" also working with type deduction without any changes, I just changed the test to try when type is unspecified.  
  
**Tested on :**   
- MSVC 2013 ( 06177-004-0444002-02057, with cl: 18.00.31101 )  
- gcc-4.9 c++03, c++11   
- clang-3.4.1 c++03  
- clang-3.5.1 c++03, c++11  
  
I think this status of the BOOST_FUSION_ADAPT* macros which doesn't need to specify the types may be used in Boost 1.58, as all what they impact as code works as before with the new type deduction feature. Documentation is also available for a while in adapted.qbk.  
  
Regarding the ADAPT_STRUCT\* macros topic, what I would like to do next if we get it in 1.58, is to make pull-requests to Boost.Spirit and other libraries having examples of ADAPT_STRUCT, to change them to the new shorter syntax.  
  
Cheers, :smile:

---

## Comment 1

> Username: daminetreg  
> Created_at: 2015-02-02 08:02:13 UTC  
> Url: https://github.com/boostorg/fusion/pull/50#issuecomment-72418549  

I tried out on the same compilers than quoted above, with the changes provided by @Flast in #49 and it works as expected.

---

## Comment 2

> Username: djowel  
> Created_at: 2015-02-08 22:55:58 UTC  
> Url: https://github.com/boostorg/fusion/pull/50#issuecomment-73437691  

What's the status of his? Are we good  to go (I.e. tested on all major compilers)?

---

## Comment 3

> Username: daminetreg  
> Created_at: 2015-02-09 08:21:44 UTC  
> Url: https://github.com/boostorg/fusion/pull/50#issuecomment-73471992  

```  
                                                                                  Hi Joel,Yes it has been tested on all major compilers, namely it works as told above on :Tested on :MSVC 2013 ( 06177-004-0444002-02057, with cl: 18.00.31101 )gcc-4.9 c++03, c++11clang-3.4.1 c++03clang-3.5.1 c++03, c++11                                                                                                                                     I feel it is in a good state. Do you want me to test more compilers? Cheers,                                                                                                                                                                                                   --Damien Buhl                                                                                                                                                                                                                From: Joel de GuzmanSent: dimanche 8 février 2015 23:56To: boostorg/fusionReply To: boostorg/fusionCc: Damien BuhlSubject: Re: [fusion] fusion_adapters : auto adaptation working also in MSVC (#50)What's the status of his? Are we good  to go (I.e. tested on all major compilers)?   
```  
  
—Reply to this email directly or view it on GitHub.

---

## Comment 4

> Username: djowel  
> Created_at: 2015-02-09 09:57:19 UTC  
> Url: https://github.com/boostorg/fusion/pull/50#issuecomment-73482754  

Great! I'm merging now. Thanks for all your hard work!

---

## Comment 5

> Username: daminetreg  
> Created_at: 2015-02-09 13:19:13 UTC  
> Url: https://github.com/boostorg/fusion/pull/50#issuecomment-73506774  

Thanks for merging. :) Shall I make a new pull-request which target develop so that we may get the changes in 1.58 ?

---

## Comment 6

> Username: djowel  
> Created_at: 2015-02-09 13:32:26 UTC  
> Url: https://github.com/boostorg/fusion/pull/50#issuecomment-73508498  

yes, that would be great!

---
