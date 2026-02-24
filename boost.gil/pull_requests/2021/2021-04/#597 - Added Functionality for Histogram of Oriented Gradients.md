# #597 Added Functionality for Histogram of Oriented Gradients [Open]

> Username: Sayan-Chaudhuri  
> Created at: 2021-04-18 03:52:51 UTC  
> Updated at: 2025-09-06 16:47:51 UTC  
> Url: https://github.com/boostorg/gil/pull/597  

Currently there are no feature descriptors in boost gil . Histogram of Oriented Gradients is a very popular feature descriptor that has been applied successfully for tasks such as  pedestrian detection in images and videos. The current implementation follows from the same in scikit-image library.  
Reference Paper- Dalal, N and Triggs, B, Histograms of Oriented Gradients for Human Detection, IEEE Computer Society Conference on Computer Vision and Pattern   
                               recognition    2005 San Diego, CA, USA, https://lear.inrialpes.fr/people/triggs/pubs/Dalal-cvpr05.pdf,:DOI:`10.1109/CVPR.2005.177`  
  
<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
<!-- What does this pull request do? -->  
  
### References  
  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: codejaeger  
> Created_at: 2021-04-18 07:40:58 UTC  
> Url: https://github.com/boostorg/gil/pull/597#issuecomment-821949045  

@Sayan-Chaudhuri you might want to take a look at the [docs](https://boostorg.github.io/gil/html/index.html) to know some of the intrinsic details about GIL. It had helped me quite a lot. Cheers!

---

## Comment 2

> Username: Sayan-Chaudhuri  
> Created_at: 2021-04-18 08:52:42 UTC  
> Url: https://github.com/boostorg/gil/pull/597#issuecomment-821957289  

@codejaeger  Thanks for your feedback. I have already gone through the docs. If you can kindly say, whether you found anything wrong that can be corrected by reading the docs, otherwise I am unable to understand the issue you are referring to.

---

## Comment 3

> Username: codejaeger  
> Created_at: 2021-04-18 11:02:38 UTC  
> Url: https://github.com/boostorg/gil/pull/597#issuecomment-821973452  

Oh no, nothing seems wrong. I had thought you did not find the docs since there is a section on image gradients and how to do it faster using native GIL constructs. I also made some comments at the relevant portions of the PR to clarify better.

---

## Review 4 [Changes requested]

> Username: lpranam  
> Created_at: 2021-04-18 17:37:37 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/597#pullrequestreview-638351873  

NO. You can't just copy the code!!! Even if everything is open source there are copyrights and license issue you just can't copy the code. Even if there are no issues of copyright or license why would we want to copy the code!? if I wanted to use scikit code I would have used scikit and not GIL.  
  
There comes many issues with copied code, the first is maintenance cost. No one knows how it works so if something goes wrong we have to spend a significant amount of time solving the problem. Reading code is harder than writing code.  
  
another issue comes in the performance, most codes are written in such a way that it is very optimised in terms of performance for their own codebase. if you just copy it they may not perform as good in GIL as they do in scikit.  
  
another problem is with format and code conventions, code is miserably formatted, template parameters are meaninglessly named, inconsistent spacing and blank lines, overall a mess in terms of code readability  
  
most importantly scikits license does not seem to be compatible with boost (not an expert in legal matters). they seem to be more restricted than boost so definitely, we can't use their code in boost  
  
and the list would go on why not to copy code and especially why not to copy a huge chunk of code...

---

## Comment 5

> Username: Sayan-Chaudhuri  
> Created_at: 2021-04-18 17:49:21 UTC  
> Url: https://github.com/boostorg/gil/pull/597#issuecomment-822031281  

@lpranam  @codejaeger  thanks for your valuable feedback

---

## Review 6 [Changes requested]

> Username: mloskot  
> Created_at: 2021-04-19 17:40:53 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/597#pullrequestreview-639138013  

`Hog_implementation.hpp` is a bad name  
  
- don't mix lower/upper case  
- word `implementation` is redundant  
  
I also suspect spacebar in your keyboard may be broken :-)

---

## Comment 7

> Username: Sayan-Chaudhuri  
> Created_at: 2021-04-19 18:08:04 UTC  
> Url: https://github.com/boostorg/gil/pull/597#issuecomment-822669050  

> NO. You can't just copy the code!!! Even if everything is open source there are copyrights and license issue you just can't copy the code. Even if there are no issues of copyright or license why would we want to copy the code!? if I wanted to use scikit code I would have used scikit and not GIL.  
>   
> There comes many issues with copied code, the first is maintenance cost. No one knows how it works so if something goes wrong we have to spend a significant amount of time solving the problem. Reading code is harder than writing code.  
>   
> another issue comes in the performance, most codes are written in such a way that it is very optimised in terms of performance for their own codebase. if you just copy it they may not perform as good in GIL as they do in scikit.  
>   
> another problem is with format and code conventions, code is miserably formatted, template parameters are meaninglessly named, inconsistent spacing and blank lines, overall a mess in terms of code readability  
>   
> most importantly scikits license does not seem to be compatible with boost (not an expert in legal matters). they seem to be more restricted than boost so definitely, we can't use their code in boost  
>   
> and the list would go on why not to copy code and especially why not to copy a huge chunk of code...  
  
No copyright issues now

---

## Comment 8

> Username: codecov[bot]  
> Created_at: 2021-04-19 19:02:09 UTC  
> Updated_at: 2025-09-06 16:47:51 UTC  
> Url: https://github.com/boostorg/gil/pull/597#issuecomment-822703926  

## [Codecov](https://app.codecov.io/gh/boostorg/gil/pull/597?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 78.72%. Comparing base ([`6e91e4b`](https://app.codecov.io/gh/boostorg/gil/commit/6e91e4bf5c1bc9b5def5ef60e3e11bc475cbf11d?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`64f25fe`](https://app.codecov.io/gh/boostorg/gil/commit/64f25fe6412ffea96f734c87f5adfaa80e0f5ac1?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 179 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #597      +/-   ##  
===========================================  
+ Coverage    78.59%   78.72%   +0.13%       
===========================================  
  Files          117      118       +1       
  Lines         5003     5034      +31       
===========================================  
+ Hits          3932     3963      +31       
  Misses        1071     1071                
```  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 9

> Username: Sayan-Chaudhuri  
> Created_at: 2021-04-19 19:17:54 UTC  
> Url: https://github.com/boostorg/gil/pull/597#issuecomment-822720740  

> `Hog_implementation.hpp` is a bad name  
>   
> * don't mix lower/upper case  
> * word `implementation` is redundant  
>   
> I also suspect spacebar in your keyboard may be broken :-)  
  
I have now referred to the codes of the existing algorithms in GIL library and have tried to format mine in the same way as those.

---

## Review 10 [Commented]

> Username: codejaeger  
> Created_at: 2021-04-22 06:34:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/597#pullrequestreview-638300567  

📁 include/boost/gil/image_processing/Hog_implementation.hpp

```diff
 175 |+                 }
 176 |+               }
 177 |+               double histogram[number_of_cells_vertical][number_of_cells_horizontal][number_of_orientations];
```

> Username: codejaeger  
> Created_at: 2021-04-18 07:28:18 UTC  
> Updated_at: 2021-04-22 06:34:31 UTC  
> Url: https://github.com/boostorg/gil/pull/597#discussion_r615356215  

There is already an existing implementation of histogram in GIL (done in the previous GSoC iteration). Could you try using that or adjust it so that it could be used? I would say it is quite well tested for use :smiley:. This would help getting feedback on what more is needed. Also, it might reduce some extra code on this PR.

> Username: Sayan-Chaudhuri  
> Created_at: 2021-04-25 04:30:17 UTC  
> Url: https://github.com/boostorg/gil/pull/597#discussion_r619748019  

I tinkered with the histogram implemented by you . However, on using that, my program runtime increased manifold. So, I was unable to use it in my program.


---

## Comment 11

> Username: Sayan-Chaudhuri  
> Created_at: 2021-04-22 20:25:22 UTC  
> Url: https://github.com/boostorg/gil/pull/597#issuecomment-825161164  

@codejaeger  Thanks for your valuable suggestion. I am now experimenting with the histogram coded by you. However, I have a question that as per my understanding, you have used an unordered map to build the histogram . The time complexity of an unordered map is O(n). Wont then my program be slower compared to me using N-Dimensional arrays as histograms where time complexity of access is almost O(1)?

---
