# #692 - Boost 1.77 merge window missed(?) with serious bugs pending [Closed]

> Username: taam  
> Created at: 2021-07-30 10:14:40 UTC  
> Updated at: 2021-08-04 11:03:38 UTC  
> Closed at: 2021-08-04 11:03:37 UTC  
> Url: https://github.com/boostorg/spirit/issues/692  

This is sort of a meta issue. Today I noticed, that 1.77 is already in progress, while I can find here at least two bugs I'd consider very serious, which are:  
  
* #668 (my own, so I might be biased regarding that one)  
* #688  
  
Both are know for a while, but there seems to be no more activity, no fix for 1.77, not even a warning in the documentation as far as I can see, leaving the library in a what I'd call dangerously broken state. This is not about a missing feature, not about a compiling issue, not about a crash, this is about seemingly working but giving a wrong result!  
  
Despite the bugs themselves, that fact that this can happen undermines my trust in the library and leaves me quite concerned. Also if I remember correctly there have been issues with the real parser before, which raises some questions.  
  
So, to ask a question, do you consider this "normal"? If not, what about consequences? Thanks!

---

## Comment 1

> Username: djowel  
> Created at: 2021-07-30 23:51:13 UTC  
> Updated at: 2021-07-30 23:54:04 UTC  
> Url: https://github.com/boostorg/spirit/issues/692#issuecomment-890256916  

@taam you know that this is open source, right? You know that you and anyone else can contribute code and documentation, right? So, why don't you do your part and contribute instead of yapping? So, I'll ask you back. Have you, at the very least, thought about contributing the warning in the documentation that you mentioned?   
  
I am no longer actively maintaining the library, nor is @hkaiser, although I still try to find some time now and then. @Kojoley has been doing a wonderful job in the past years, esp. on maintenance. I offered a solution in one of the issues you linked to. Open source is supposed to be a community effort. We are all doing this for free. You can also do your part, as much as you seem to require someone else to do their part. PRs are always welcome, and that includes code and docs.

---

## Comment 2

> Username: taam  
> Created at: 2021-08-02 15:00:20 UTC  
> Url: https://github.com/boostorg/spirit/issues/692#issuecomment-891096858  

A (hopefully good) bug report is a small contribution as well I think, code would be better, but in this case I do not feel nearly qualified to tackle this. I have not seriously considered to add a warning, hoping that the issue would be solved before the next release. But as you asked I now invested some more hours (mostly trying so get the Boost docs compiled to HTML somehow which seems to be pretty difficult), here's my try: #693  
  
However, this is o.c. not about expecting something from someone individually (like in personal responsibility), it's about what an outsider like me might expect (like in hoping what works and what doesn't) from a well known and widely used project like Boost. So without knowing much about Boost internals, I was expecting/hoping that there are processes that (try to) prevent situations like the one I initially described, so I opened this issue to ask if it was maybe just bad luck. My intention certainly was not to offend anyone, but I think this is concerning and important, as I'm pretty sure lots of people are using this in production code.

---

## Comment 3

> Username: Kojoley  
> Created at: 2021-08-02 21:02:44 UTC  
> Url: https://github.com/boostorg/spirit/issues/692#issuecomment-891330375  

> A (hopefully good) bug report is a small contribution as well  
  
It does, thanks for opening #668, but what is the purpose of *this* ticket?  
  
> I think, code would be better, but in this case I do not feel nearly qualified to tackle this. I have not seriously considered to add a warning, hoping that the issue would be solved before the next release.  
  
I spend several days digging the issue, the root cause seems simple, but fix is not.  
  
> But as you asked I now invested some more hours (mostly trying so get the Boost docs compiled to HTML somehow which seems to be pretty difficult), here's my try: #693  
  
The docs system setup is indeed a little-bit frustrating, but it is not on Spirit.  
  
> However, this is o.c. not about expecting something from someone individually (like in personal responsibility), it's about what an outsider like me might expect (like in hoping what works and what doesn't) from a well known and widely used project like Boost. So without knowing much about Boost internals, I was expecting/hoping that there are processes that (try to) prevent situations like the one I initially described, so I opened this issue to ask if it was maybe just bad luck.  
  
I do not understand why a question about the already reported issue needs a separate ticket.  
  
> My intention certainly was not to offend anyone, but I think this is concerning and important, as I'm pretty sure lots of people are using this in production code.  
  
I see what you mean, but the code is distributed under Boost license, and I am pretty sure the license is very clear on terms of usage. I think @djowel painted the picture very clearly.  
  
We do what we can with resources we have, and the only resources I have are write access to the repository and my spare time. I am helping anyone to get changes into the project reviewed and merged, but I cannot give ETA on any bugs been fixes.

---

## Comment 4

> Username: taam  
> Created at: 2021-08-04 11:03:37 UTC  
> Url: https://github.com/boostorg/spirit/issues/692#issuecomment-892566981  

> but what is the purpose of _this_ ticket?  
  
The purpose of this ticket was to ask, if what was described is considered "normal" or not. From what I've seen, I would now answer that with "yes".  
  
> > However, this is o.c. not about expecting something from someone individually (like in personal responsibility), it's about what an outsider like me might expect (like in hoping what works and what doesn't) from a well known and widely used project like Boost. So without knowing much about Boost internals, I was expecting/hoping that there are processes that (try to) prevent situations like the one I initially described, so I opened this issue to ask if it was maybe just bad luck.  
>   
> I do not understand why a question about the already reported issue needs a separate ticket.  
  
Because it's about something very different, that's what I was trying to explain there. To give an arbitrary example: It could be part of preparing a new Boost release to actively require a reaction from each library maintainer to make sure, that each library is in acceptable state for the next release. It seems I was too naive in that regard, sorry.
