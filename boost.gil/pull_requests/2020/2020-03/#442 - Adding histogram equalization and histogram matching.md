# #442 [WIP] Adding histogram equalization and histogram matching [Closed]

> Username: NEDJIMAbelgacem  
> Created at: 2020-03-05 23:13:36 UTC  
> Updated at: 2021-01-25 23:31:38 UTC  
> Closed at: 2021-01-25 22:35:18 UTC  
> Url: https://github.com/boostorg/gil/pull/442  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
- Histogram equalization implementation.  
- Histogram matching (specification) implementation.  
- Histogram visualisation as .jpg image.  
  
This is part of my competency test solution for GSoC 2020 and I would be happy to improve it according to feedback.  
  
### References  
  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
https://en.wikipedia.org/wiki/Histogram_equalization  
https://en.wikipedia.org/wiki/Histogram_matching  
The great book https://link.springer.com/book/10.1007/978-1-84800-191-6  
Thank you for recommending the book it is very good.  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [ ] Add test case(s)  
- [ ] Make a better generic implementation  
- [ ] Downscale histogram visualization image.  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: mloskot  
> Created_at: 2020-03-05 23:26:06 UTC  
> Url: https://github.com/boostorg/gil/pull/442#issuecomment-595496348  

Is this by any chance related to GSoC 2020?

---

## Comment 2

> Username: NEDJIMAbelgacem  
> Created_at: 2020-03-05 23:28:51 UTC  
> Url: https://github.com/boostorg/gil/pull/442#issuecomment-595497128  

@mloskot Yes it is, sorry I forgot to mention that in the description.

---

## Comment 3

> Username: mloskot  
> Created_at: 2020-03-05 23:29:44 UTC  
> Url: https://github.com/boostorg/gil/pull/442#issuecomment-595497376  

If this is your solution for the competency test, then please also read https://www.boost.org/community/gsoc.html

---

## Comment 4

> Username: NEDJIMAbelgacem  
> Created_at: 2020-03-05 23:46:20 UTC  
> Url: https://github.com/boostorg/gil/pull/442#issuecomment-595502061  

Yes, I already got through GSoC page and all. Can you please clarify what I misunderstood ?

---

## Comment 5

> Username: mloskot  
> Created_at: 2020-03-05 23:54:28 UTC  
> Url: https://github.com/boostorg/gil/pull/442#issuecomment-595504160  

If this PR is related to GSoC and the competency test, it would be helpful if the description states that, for example, see #435.  
  
As we already have received similar PRs, i.e. GSoC and competency tests, please find them and read through the comments to see what to expect w.r.t. to your PR For example, see https://github.com/boostorg/gil/pull/435#issuecomment-588371700 and #430

---

## Comment 6

> Username: mloskot  
> Created_at: 2021-01-25 22:35:18 UTC  
> Url: https://github.com/boostorg/gil/pull/442#issuecomment-767158103  

Thanks once again for your efforts, but this functionality has been developed as part of the [[gsoc2020] Histogram Computation and Algorithms ](https://github.com/boostorg/gil/projects/7) project.  
  
You are more than welcome to help us improve the histogram support.

---
