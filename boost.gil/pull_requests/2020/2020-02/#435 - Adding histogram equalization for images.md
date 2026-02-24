# #435 Adding histogram equalization for images [Closed]

> Username: codejaeger  
> Created at: 2020-02-19 08:31:29 UTC  
> Updated at: 2020-08-25 17:37:26 UTC  
> Closed at: 2020-08-25 17:37:26 UTC  
> Url: https://github.com/boostorg/gil/pull/435  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
[Histogram equalization](https://en.wikipedia.org/wiki/Histogram_equalization) is a contrast enhancement algorithm for images whose pixel intensities are concentrated in small regions.  
Currently this implementation also supports histogram equalization for images with multiple channels under the assumption the input channels are independent. Images with dependent channels (like RGB) should be be converted to an ([YCbCr](https://en.wikipedia.org/wiki/YCbCr) for RGB) independent color space before using histogram equalization.  
  
### Motivation  
This PR is proposed as a solution to the competency test for GSoC 2020. I chose histogram equalization though it was not in the proposed topics pages because if accepted it could be a starting step to implementing important image processing algorithms like [CLAHE](https://en.wikipedia.org/wiki/Adaptive_histogram_equalization) for cases where histogram equalization might not give good results.  
  
  
### References  
  
Application of histogram equalization on some images - [Github link](https://github.com/codejaeger/he_images)  
  
### Tasklist  
  
- [x] Add basic tests  
- [x] Ensure all CI builds pass  
- [x] Add appropriate commenting  
- [ ] Review and approve  
- [x] Updating PR Description

---

## Comment 1

> Username: mloskot  
> Created_at: 2020-02-19 18:26:57 UTC  
> Updated_at: 2020-02-19 18:28:47 UTC  
> Url: https://github.com/boostorg/gil/pull/435#issuecomment-588371700  

Thank you for the PR and attempt to propose new feature.  
  
First, to save us all time and avoid nitpicking reviews (naming, formatting, whitespaces, style),  
could you please auto-review your PR against our development guidelines linked here https://github.com/boostorg/gil/wiki/Guidelines (i.e. `CONTRIBUTING.md` and suggested `#include` ordering)?  
  
Second, I'd strongly recommend you to read through review and comments posted to PR #430. This should give an idea how reviews run, what comments to expect.  
  
Third, as you can read in comments to PR #430, please be aware that for the competency test itself it is enough to present C++ code, but to get your feature accepted, it usually takes more effort (discussing design and interface choices, code comments, detailed tests, documentation, histogram operations may better fit general purpose part of GIL and not necessarily the `image_processing` module, etc.).   
IOW, you are not expected to get it all done as part of the competency test.  
  
Meanwhile, you could also offer some more details on what algorithm the PR proposes (any thing that can be named, referenced, etc.), updating PR description is fine. Code comments and documentation will come later if you decided to continue working on it.  
  
/cc @stefanseefeld

---

## Comment 2

> Username: codejaeger  
> Created_at: 2020-02-19 19:05:40 UTC  
> Url: https://github.com/boostorg/gil/pull/435#issuecomment-588392532  

Thanks @mloskot for the feedback . I will make the necessary changes according to the guidelines specified and will have a look at  PR #430 .  
As for   
  
>  histogram operations may better fit general purpose part of GIL and not necessarily the image_processing module, etc.)  
  
I was of the same opinion , but a bit unsure of how to implement that. If I can get some further insights on this, so that my changes conform to Boost:gil design choices I would be glad to make those changes.   
I will also make the PR more descriptive regarding the proposed solution for the algorithm.

---

## Comment 3

> Username: mloskot  
> Created_at: 2020-02-19 19:12:57 UTC  
> Url: https://github.com/boostorg/gil/pull/435#issuecomment-588395931  

> > histogram operations may better fit general purpose part of GIL and not necessarily the image_processing module, etc.)  
>   
> I was of the same opinion , but a bit unsure of how to implement that.  
  
For example, core histogram utilities could live in `include/boost/gil/histogram.hpp`, then additional, more task-specific features could be offered as extension(s) in  `include/boost/gil/extension/histogram/*.hpp`.   
  
For example, if someone comes up with support for [Boost.Histogram](https://www.boost.org/libs/histogram/doc/html/index.html) as output as well as input from/to GIL histogram utilities and algorithms, such features could be hosted in the extension.  
  
/cc @stefanseefeld may have some insights on the bigger picture of code structuring

---

## Comment 4

> Username: mloskot  
> Created_at: 2020-02-19 21:38:27 UTC  
> Url: https://github.com/boostorg/gil/pull/435#issuecomment-588484480  

@codejaeger   
> Images with dependent channels (like RGB) can be be converted to YCbCr color space before using histogram equalization.  
  
One more thing to that note, I think it would be good to clarify (eventually in comments/docs) the definition of (in)dependent channels.  
  
For example, if it is equivalent to (or taken from) this one given in the [RGB CHANNELS (IN)DEPENDENCE IN PHASE CONTRAST MICROPHOTOGRAPHY](https://www.researchgate.net/publication/255655780_RGB_CHANNELS_INDEPENDENCE_IN_PHASE_CONTRAST_MICROPHOTOGRAPHY) ([alternative PDF](http://dsp.vscht.cz/konference_matlab/MATLAB09/prispevky/106_urban.pdf)), it may be worth to refer/cite that paper:  
  
```  
If the detection system is for example a standard photograph, each of the three channels (Red, Greenand Blue) carries different - partly **independent** - information which may be used for state definition.  
(...)  
In case of colour images are histogram functions normally computed **independently** for each colour channel, without considerations about relations between each other.  
```  
  
------  
  
By the way, this also reminded me GIL-related question on StackOverflow, [Histogram equalization upon RGB images? RGB ouput possible?](https://stackoverflow.com/q/24350423/151641), you may want to score some credits there :-)

---

## Comment 5

> Username: codejaeger  
> Created_at: 2020-02-19 22:15:36 UTC  
> Url: https://github.com/boostorg/gil/pull/435#issuecomment-588500146  

@mloskot   
I went through the guidelines as suggested and have taken care of most of those trivial changes . I guess there could still be some issues which might come up while reviewing . I will add more advanced testcases shortly.

---

## Comment 6

> Username: mloskot  
> Created_at: 2020-02-19 22:26:49 UTC  
> Url: https://github.com/boostorg/gil/pull/435#issuecomment-588504989  

I will have a detailed look at later this week or weekend.  
  
Meanwhile, as I explained in https://github.com/boostorg/gil/pull/430#issuecomment-581576130  
  
> you are not expected to finish-polish the PR in order to count it for a completed competency test solution (...)  
> Although you can postpone addressing further reviews and completion of this PR for later (e.g. during GSoC), you are very welcome to continue working on to make it ready for merging. It's your call.  
  
At this stage it's more important, I think, to gain basic experience about [Boost development[(https://www.boost.org/development/index.html) (right-hand side links), GIL itself and its development, Git & GitHub operations, and finally think about what you'd like to develop during GSoC (important for writing your proposal).

---

## Comment 7

> Username: codejaeger  
> Created_at: 2020-02-20 07:35:32 UTC  
> Url: https://github.com/boostorg/gil/pull/435#issuecomment-588683221  

> At this stage it's more important, I think, to gain basic experience about [Boost development[(https://www.boost.org/development/index.html) (right-hand side links), GIL itself and its development, Git & GitHub operations, and finally think about what you'd like to develop during GSoC (important for writing your proposal).  
  
Thanks @mloskot, I will do so right away.

---

## Comment 8

> Username: codejaeger  
> Created_at: 2020-03-02 05:41:05 UTC  
> Updated_at: 2020-03-07 19:16:59 UTC  
> Url: https://github.com/boostorg/gil/pull/435#issuecomment-593229890  

Accidentally closed the PR by mistake,   
it seems that this PR need refurnishing before it can be even actually accepted as a partial solution to the histogram issue in GIL. So , I think I will make the changes as proposed in the [wiki](https://github.com/boostorg/wiki/wiki/Google-Summer-of-Code%3A-2020) page.

---

## Comment 9

> Username: mloskot  
> Created_at: 2020-03-02 09:54:48 UTC  
> Url: https://github.com/boostorg/gil/pull/435#issuecomment-593318296  

@codejaeger First, this PR is a competency test solution. If your project is accepted, you will have chance to work on it during GSoC and if not you will be welcome to continue working on it.  
One way or another, this PR can be improved according to the suggestions on the Wiki page, later. I wouldn't worry about it now.

---

## Comment 10

> Username: mloskot  
> Created_at: 2020-08-25 13:24:27 UTC  
> Updated_at: 2020-08-25 13:24:36 UTC  
> Url: https://github.com/boostorg/gil/pull/435#issuecomment-680022822  

@codejaeger What are we going to do with this PR? Shall this be closed as replaced by the later PRs?

---

## Comment 11

> Username: codejaeger  
> Created_at: 2020-08-25 17:25:09 UTC  
> Updated_at: 2020-08-25 17:26:52 UTC  
> Url: https://github.com/boostorg/gil/pull/435#issuecomment-680162696  

Yes @mloskot we can close this as the other PR #514 aims to provide the same feature and is the one I am currently working on.

---
