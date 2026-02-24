# #593 Add distance transform functionality [Closed]

> Username: harshitpant1  
> Created at: 2021-04-02 19:41:13 UTC  
> Updated at: 2021-04-03 04:46:33 UTC  
> Closed at: 2021-04-03 04:37:25 UTC  
> Url: https://github.com/boostorg/gil/pull/593  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
Adds Distance Transform functionality to gil.  
  
### References  
  
Precise Euclidean distance transform -   
http://www.theoryofcomputing.org/articles/v008a019/v008a019.pdf   
  
Manhattan and chessboard distance transforms -   
Principles of Digital Image Processing - core Techniques by Wilhelm Burger, Mark J.Burge.  
  
Approximate Euclidean distance transform -   
http://www.cmm.mines-paristech.fr/~marcoteg/cv/publi_pdf/MM_refs/1986_Borgefors_distance.pdf  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-04-03 04:11:26 UTC  
> Url: https://github.com/boostorg/gil/pull/593#issuecomment-812807274  

# [Codecov](https://codecov.io/gh/boostorg/gil/pull/593?src=pr&el=h1) Report  
> Merging [#593](https://codecov.io/gh/boostorg/gil/pull/593?src=pr&el=desc) (f2efe8a) into [develop](https://codecov.io/gh/boostorg/gil/commit/2ad274cc81f1613a0dc723a6c87a4c48dea9e35f?el=desc) (2ad274c) will **increase** coverage by `0.59%`.  
> The diff coverage is `97.05%`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #593      +/-   ##  
===========================================  
+ Coverage    78.72%   79.32%   +0.59%       
===========================================  
  Files          118      119       +1       
  Lines         5034     5204     +170       
===========================================  
+ Hits          3963     4128     +165       
- Misses        1071     1076       +5       
```

---

## Comment 2

> Username: harshitpant1  
> Created_at: 2021-04-03 04:37:25 UTC  
> Url: https://github.com/boostorg/gil/pull/593#issuecomment-812809728  

Closing this and will make another PR with commits against latest develop branch.

---
