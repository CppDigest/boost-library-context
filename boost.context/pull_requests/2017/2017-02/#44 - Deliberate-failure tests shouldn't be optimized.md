# #44 Deliberate-failure tests shouldn't be optimized [Merged]

> Username: aargor  
> Created at: 2017-02-14 20:26:45 UTC  
> Updated at: 2017-02-17 17:57:04 UTC  
> Merged at: 2017-02-14 20:55:22 UTC  
> Closed at: 2017-02-14 20:55:22 UTC  
> Url: https://github.com/boostorg/context/pull/44  

A few tests designed to deliberately fail appeal to UB (undefined  
behavior), but as the MSVC optimizer improves we will take advantage of  
that to remove UB statements. In order for deliberate failures to occur,  
the tests must have the optimizer turned off.

---

## Comment 1

> Username: aargor  
> Created_at: 2017-02-14 20:27:26 UTC  
> Url: https://github.com/boostorg/context/pull/44#issuecomment-279825648  

Hello, I'm from the MSVC++ compiler team. I created the above pull request because as we're improving the optimizer, we've tested internally that a few tests will fail as we more successfully optimize in the presence of undefined behavior. The team I'm on expects that these tests will fail with the soon-to-be released VS2017, because we optimize away the code that's designed to trigger the faults.  
  
I hope this is the right way to contribute back to the Boost project. Please let me know if there's anything else I should do.  
  
(I'll add that I'm pull-requesting similar changes in the boostorg/test project as well.)  
  
Thanks,  
Aaron Gorenstein  
Microsoft Visual C++

---

## Comment 2

> Username: olk  
> Created_at: 2017-02-14 20:55:16 UTC  
> Url: https://github.com/boostorg/context/pull/44#issuecomment-279832964  

Hello Aaron, I appreciate your contribution.  
Do you know if VS2017 will be available as free-of-charge for developers?

---

## Comment 3

> Username: aargor  
> Created_at: 2017-02-15 00:04:03 UTC  
> Url: https://github.com/boostorg/context/pull/44#issuecomment-279876546  

Thanks! I'm glad it met your bar to be merged!  
  
Regarding VS2017 pricing: I really don't know for the release. The RC is free to try out, of course, and I know VS2015 Community edition is free under certain conditions. I will try to follow up with some colleagues and see if I can find out and share more.

---

## Comment 4

> Username: aargor  
> Created_at: 2017-02-17 17:34:54 UTC  
> Url: https://github.com/boostorg/context/pull/44#issuecomment-280715415  

Hello, I just spoke with a colleague: we are definitely releasing a VS2017 Community edition. It is completely free, but I believe the license is something like that can be used in orgs up to 250 individuals. I hope that's something you're interested in looking into :).  
  
Thanks again,  
Aaron

---

## Comment 5

> Username: olk  
> Created_at: 2017-02-17 17:57:04 UTC  
> Url: https://github.com/boostorg/context/pull/44#issuecomment-280721111  

Hello, nice that VS2017 will be made available as community edition - I'll use it to keep my boost libraries up-to-date on Windows.  
Thank you too,  
Oliver

---
