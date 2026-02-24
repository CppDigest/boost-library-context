# #436 Clarify use of 'using' in Jamfiles vs configuration files [Merged]

> Username: mloskot  
> Created at: 2019-05-09 09:50:43 UTC  
> Updated at: 2021-10-02 20:59:41 UTC  
> Merged at: 2019-05-21 03:30:21 UTC  
> Closed at: 2019-05-21 03:30:21 UTC  
> Url: https://github.com/boostorg/build/pull/436  



---

## Comment 1

> Username: grafikrobot  
> Created_at: 2019-05-09 11:58:09 UTC  
> Url: https://github.com/boostorg/build/pull/436#issuecomment-490873355  

The `using` statement is actually perfectly usable in non-configuration source. And it's the currently preferred way to declare that you are using a tool from project jamfiles. Did you run into an instance where doing that didn't work?

---

## Comment 2

> Username: grisumbras  
> Created_at: 2019-05-09 12:06:22 UTC  
> Url: https://github.com/boostorg/build/pull/436#issuecomment-490875845  

While it is usable, wouldn't it prevent other people to configure used tools in some other way?

---

## Comment 3

> Username: grafikrobot  
> Created_at: 2019-05-09 12:09:26 UTC  
> Url: https://github.com/boostorg/build/pull/436#issuecomment-490876785  

It shouldn't prevent it. The usual method for projects is to use the default tool. I.e. `using foo ;`. Which should have the effect of picking up whatever a user has declared in one of the `*-config.jam` files as those get invoked first.

---

## Comment 4

> Username: grisumbras  
> Created_at: 2019-05-09 12:17:30 UTC  
> Url: https://github.com/boostorg/build/pull/436#issuecomment-490879211  

It would only work that way, if the tool's module was written to handle it that way, correct?

---

## Comment 5

> Username: grisumbras  
> Created_at: 2019-05-09 12:27:14 UTC  
> Url: https://github.com/boostorg/build/pull/436#issuecomment-490882138  

I just checked with `using gcc ;` and b2 errors out complaining about duplicate initialization.

---

## Comment 6

> Username: swatanabe  
> Created_at: 2019-05-09 12:56:43 UTC  
> Url: https://github.com/boostorg/build/pull/436#issuecomment-490892368  

AMDG  
  
On 5/9/19 6:17 AM, Dmitry wrote:  
> It would only work that way, if the tool's module was written to handle it that way, correct?   
>   
  
Yep.  Some tools do handle it, just not compilers.  Incidentally,  
the main reason that the compilers don't handle default initialization  
is that they don't need to.  Boost.Build has hard-coded default compiler  
initialization.  I would actually prefer it if we required Jamfiles to  
have:  
  
using cxx ;  
  
or some such.  
  
In Christ,  
Steven Watanabe

---

## Comment 7

> Username: grisumbras  
> Created_at: 2019-05-09 13:09:53 UTC  
> Url: https://github.com/boostorg/build/pull/436#issuecomment-490897330  

I checked the toolset module authoring guidelines and, IMO, they suggest that  
  
```  
using foo : 1 : ... ;  
using foo : 2 : ... ;  
using foo ;  
```  
should *not* work.   
So, maybe the guidelines should be changed a bit?

---

## Comment 8

> Username: mloskot  
> Created_at: 2019-05-09 13:47:21 UTC  
> Updated_at: 2019-05-09 13:47:54 UTC  
> Url: https://github.com/boostorg/build/pull/436#issuecomment-490912145  

I'm not quite capable to provide any relevant input to the wider discussion that has rolled out.  
  
> The using statement is actually perfectly usable in non-configuration source  
  
Meanwhile, what about updating this PR and flip the note to read  
  
```  
TIP: You should prefer to use the  `using` rule in project's `Jamfile` file.  
```  
  
to improve one side of the issue. Shall I?  
  
> Did you run into an instance where doing that didn't work?  
  
No, I didn't. It was suggested to me in [this thread on Slack](https://cpplang.slack.com/archives/C27KZLB0X/p1557350198230000). It quite made sense to me, so I assumed it is correct and decided to add relevant update to the docs.

---

## Comment 9

> Username: swatanabe  
> Created_at: 2019-05-09 14:01:56 UTC  
> Url: https://github.com/boostorg/build/pull/436#issuecomment-490917791  

AMDG  
  
On 5/9/19 7:47 AM, Mateusz Łoskot wrote:  
> I'm not quite capable to provide any relevant input to the wider discussion that has rolled out.  
>   
>> The using statement is actually perfectly usable in non-configuration source  
>   
> Meanwhile, what about updating this PR and flip the note to read  
>   
> ```  
> TIP: You should prefer to use the  `using` rule in project's `Jamfile` file.  
> ```  
>   
  
That's also wrong.  The current state is:  
- Put `using tool ;` with no other argument in a Jamfile that  
  needs `tool`, provided that the tool supports this usage.  
- In all other cases, `using` should be in a configuration file.  
  
The general principles behind this are:  
a) Jamfiles should be portable.  Config files are system specific.  
b) If you have a standard configuration (i.e. all tools are  
   installed by your platform's package manager), config files  
   should be unnecessary.  
  
Note that this is only advisory.  Boost.Build doesn't enforce it.  
  
>> Did you run into an instance where doing that didn't work?  
>   
> No, I didn't. It was suggested to me in [this thread on Slack](https://cpplang.slack.com/archives/C27KZLB0X/p1557350198230000). It quite made sense to me, so I assumed it is correct and decided to add relevant update to the docs.  
>   
  
In Christ,  
Steven Watanabe

---

## Comment 10

> Username: mloskot  
> Created_at: 2019-05-09 15:51:55 UTC  
> Url: https://github.com/boostorg/build/pull/436#issuecomment-490961113  

> The current state is:  
> - Put `using tool ;` with no other argument in a Jamfile that needs `tool`, provided that the tool supports this usage.  
>   
> - In all other cases, `using` should be in a configuration file.   
  
It makes perfect sense. Why not to add these notes to the docs? Even a single `TIP` would make things clearer, IMO.

---

## Comment 11

> Username: grafikrobot  
> Created_at: 2019-05-12 15:12:21 UTC  
> Url: https://github.com/boostorg/build/pull/436#issuecomment-491604106  

Care to adjust the PR for those notes instead?

---

## Comment 12

> Username: mloskot  
> Created_at: 2019-05-14 17:47:39 UTC  
> Url: https://github.com/boostorg/build/pull/436#issuecomment-492339620  

@grafikrobot  I do care, but I'm attending a hackathon in Minneapolis, so may not be able to do it before I'm back home  next week.

---

## Comment 13

> Username: mloskot  
> Created_at: 2019-05-20 22:38:19 UTC  
> Url: https://github.com/boostorg/build/pull/436#issuecomment-494174913  

@grafikrobot I've modified the note according to the revelations discussed in comments. Is it any better, anything to correct?

---

## Comment 14

> Username: github-actions[bot]  
> Created_at: 2021-10-02 20:59:40 UTC  
> Url: https://github.com/boostorg/build/pull/436#issuecomment-932819681  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
