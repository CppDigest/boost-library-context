# #379 Added definition of gaussian adaptive threshold [Merged]

> Username: miralshah365  
> Created at: 2019-08-11 18:47:04 UTC  
> Updated at: 2022-06-29 20:45:10 UTC  
> Merged at: 2019-08-19 20:34:12 UTC  
> Closed at: 2019-08-19 20:34:12 UTC  
> Url: https://github.com/boostorg/gil/pull/379  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
New threshold adaptive method added.  
  
This method allows the user to set threshold values according to neighbors. The threshold value is found by calculating the gaussian value of all the neighbors including pixel itself. This gaussian value is then treated as a threshold and the binary threshold is performed.  
  
<!-- What does this pull request do? -->  
  
### References  
closes #316  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [ ] Add test case(s) - to be done in separate PR as per https://github.com/boostorg/gil/pull/379#issuecomment-522388839  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2019-08-18 15:03:03 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/379#pullrequestreview-276282890  

@miralshah365 Are you planning to add any test cases?  
  
This can be merged as it is, but either the description states that a test is coming soon as separate PR or a test is added to this PR.

---

## Comment 2

> Username: miralshah365  
> Created_at: 2019-08-19 02:29:07 UTC  
> Updated_at: 2019-08-19 02:29:19 UTC  
> Url: https://github.com/boostorg/gil/pull/379#issuecomment-522388839  

@mloskot I will send a separate PR to add tests for both the adaptive thresholds

---

## Review 3 [Approved]

> Username: mloskot  
> Created_at: 2019-08-19 20:02:48 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/379#pullrequestreview-276783361  

@miralshah365 Feel free to merge

---

## Comment 4

> Username: marco-langer  
> Created_at: 2022-06-29 19:48:54 UTC  
> Url: https://github.com/boostorg/gil/pull/379#issuecomment-1170420221  

@mloskot The sudoku threshold example image in this PR is the same as the one used in the [official OpenCV thresholding tutorial](https://docs.opencv.org/4.6.0/d7/d4d/tutorial_py_thresholding.html).  
  
Do you think this is a problem regarding copyright?

---

## Comment 5

> Username: mloskot  
> Created_at: 2022-06-29 20:45:00 UTC  
> Updated_at: 2022-06-29 20:45:10 UTC  
> Url: https://github.com/boostorg/gil/pull/379#issuecomment-1170476404  

@marco-langer This is a very good question which I have kind of avoided to address for long time planning to  
- get rid of all binary test data from `boostorg/gil`  
- only allow ASCII test images inside `boostorg/gil`, see also https://github.com/boostorg/gil/issues/359  
- offload all test images to external repository as discussed in https://github.com/boostorg/gil/issues/462  
  
Then, `boostorg/gil` would be just a client/user of collection(s) of test images.  
  
For now, I don't know what is the answer to your question.  
Possibly, test images used by OpenCV have also external provenance ¯\_(ツ)_/¯

---
