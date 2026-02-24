# #446 [WIP] Adding feature 'Gaussian Filter' [Closed]

> Username: laxsuryavanshi  
> Created at: 2020-03-09 20:13:32 UTC  
> Updated at: 2020-09-09 09:14:28 UTC  
> Closed at: 2020-09-09 09:07:23 UTC  
> Url: https://github.com/boostorg/gil/pull/446  

### Description  
### Gaussian Filter  
The Gaussian smoothing operator is a 2-D convolution operator that is used to `blur' images and remove detail and noise.  
  
### References  
https://homepages.inf.ed.ac.uk/rbf/HIPR2/gsmooth.htm  
  
### Further Development during GSoC  
* Add more algorithms  
* Documentation of Code  
* Review and approval from mentor and merge of this PR  
  
### Tasklist  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: lpranam  
> Created_at: 2020-03-10 13:48:54 UTC  
> Url: https://github.com/boostorg/gil/pull/446#issuecomment-597095536  

Please stop creating new PR every time for same thing after modification

---

## Comment 2

> Username: mloskot  
> Created_at: 2020-03-10 14:07:45 UTC  
> Url: https://github.com/boostorg/gil/pull/446#issuecomment-597105210  

Yes, good point. I also wondered what's that about but did not checked it in details, just labelled.

---

## Comment 3

> Username: mloskot  
> Created_at: 2020-03-10 14:25:00 UTC  
> Url: https://github.com/boostorg/gil/pull/446#issuecomment-597114273  

Yes, I asked for separate PR fixing a typo, but it should be handled quite easily and without necessity of separate PR.  
  
> since I commited everything on develop branch, I find it difficult to separate out   
  
Before re-creating PRs, you could ask how to do it, how to extract change from existing PR to submit as separate PR. Chances are, somebody would help you and you could have learned it for future situations that may be common during GSoC as well as during your activities on GitHub generally.

---

## Review 4 [Changes requested]

> Username: lpranam  
> Created_at: 2020-03-10 14:41:12 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/446#pullrequestreview-372016216  

I think gaussian kernel generation requires a separate PR. You also need to submit a test for Gaussian kernel first.

---

## Comment 5

> Username: mloskot  
> Created_at: 2020-03-10 14:59:30 UTC  
> Url: https://github.com/boostorg/gil/pull/446#issuecomment-597133339  

In one of other PRs, I think I suggested to add a utility converting between 1D and 2D kernels if separability is needed but 2D kernel is in hand. Just reminding the idea, in case it's a good idea. There may be better ways to solve it perhaps.

---

## Comment 6

> Username: lpranam  
> Created_at: 2020-03-10 15:29:18 UTC  
> Url: https://github.com/boostorg/gil/pull/446#issuecomment-597149817  

@mloskot I thought of that and after some research, I came to know that directly creating 1d kernel can be performance efficient

---

## Comment 7

> Username: mloskot  
> Created_at: 2020-03-10 18:39:20 UTC  
> Url: https://github.com/boostorg/gil/pull/446#issuecomment-597247997  

@lpranam The kernel conversion idea follows your https://github.com/boostorg/gil/pull/443#discussion_r389305321 about re-using this function  
  
https://github.com/boostorg/gil/blob/f33128e20edd676728f826107adac19c22be9651/include/boost/gil/image_processing/numeric.hpp#L129   
  
If this function can not be reused or its outcome can not be (efficiently) converted to usable form, perhaps this function should be reworked to generate 1D or 2D kernels.  
  
My motivation is to look for possible pattern common for number of use cases to avoid breeding too many (public!) generator functions, especially for the same filters.  If we could have some neat common D-agnostic _interface_ for kernel generators...  
  
-----  
  
@laxsuryavanshi If I may, I'd suggest you to _focus more on your GSoC proposal_ than contributing new features to GIL before GSoC starts. If you participate in GSoC, you will have time to get those features well designed and implemented. I understand you may be eager to sling PRs with new features and ideas, but this is not the best time now to propose significant features, before GSoC.  
As you see, it takes time to discuss and agree on things and we don't have that time now, so your PRs will likely remain unanswered.   
  
To summarise,   
- Submit PR with your competency test if you want to have it tested and reviewed, but please keep in mind my other advice in https://github.com/boostorg/gil/pull/443#issuecomment-596133766 (keep in mind https://www.boost.org/community/gsoc.html).  
- If you are still eager to contribute something, feel free to add new test cases, help fix warnings, improve quality of existing code, etc. - submissions which are fairly easy to review and get merged independently from GSoC

---

## Comment 8

> Username: mloskot  
> Created_at: 2020-03-10 19:48:32 UTC  
> Url: https://github.com/boostorg/gil/pull/446#issuecomment-597280160  

> I'd like to contribute to GIL even if I didn't selected cause this will only increase my programming skills.   
  
Great and you're welcome to contribute. It's just that it usually takes time until a contributor becomes familiar with any non-trivial codebase, so her/his is capable to propose new features designed and implemented so that they fit the whole picture well.  
  
p.s. FYI, Sir is [not necessary](https://medium.com/@akshikawijesundara/google-summer-of-code-dos-and-do-not-s-896f05e29ac0) really :)

---

## Comment 9

> Username: lpranam  
> Created_at: 2020-03-11 05:28:30 UTC  
> Url: https://github.com/boostorg/gil/pull/446#issuecomment-597451040  

> @lpranam The kernel conversion idea follows your [#443 (comment)](https://github.com/boostorg/gil/pull/443#discussion_r389305321) about re-using this function  
  
That's when @laxsuryavanshi chooses to use 2D Gaussian kernel but if he chooses to use 1D Gaussian kernel a newer implementation can generate 1d kernel faster then converting from 2d to 1d.  
  
  
> My motivation is to look for possible pattern common for number of use cases to avoid breeding too many (public!) generator functions, especially for the same filters. If we could have some neat common D-agnostic _interface_ for kernel generators...  
  
I think we can have overloads for 1D and 2D kernels which can be handy for the user.

---

## Comment 10

> Username: mloskot  
> Created_at: 2020-03-11 07:08:14 UTC  
> Updated_at: 2020-03-11 08:03:06 UTC  
> Url: https://github.com/boostorg/gil/pull/446#issuecomment-597477329  

Yes an overload taking in/out kernel parameter sounds fine.   
  
I'm also a bit allergic to sprinkling `get_*` free functions over the public interface.  
  
https://github.com/boostorg/gil/blob/49947a5a689448b2dfb3147f4a6f33e99787a85b/include/boost/gil/extension/numeric/kernel.hpp#L333-L334  
  
Even for such a simple functionality, we should be quite considerate how to shape the interface of it, also because of https://martinfowler.com/bliki/TwoHardThings.html, unless it's going to live inside `namespace gil::detail`, then we can afford sloppy.

---
