# #554 Port code coverage with codecov [Closed]

> Username: meshtag  
> Created at: 2021-01-29 14:21:47 UTC  
> Updated at: 2021-01-31 14:59:47 UTC  
> Closed at: 2021-01-29 21:08:01 UTC  
> Url: https://github.com/boostorg/gil/pull/554  

### Description  
  
This pull request intends to port code coverage using codecov from travis ci pipeline to github actions. Original script for travis ci pipeline was written by @codejaeger .  
  
### References  
 #548 #532  
  
### Tasklist  
  
- [ ] Add test case(s)  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: mloskot  
> Created_at: 2021-01-30 20:17:18 UTC  
> Updated_at: 2021-01-30 20:17:36 UTC  
> Url: https://github.com/boostorg/gil/pull/554#issuecomment-770274682  

@meshtag It's a good if author of PR explains reasons of closing it w/o merge, just to avoid confusion and clear possible questions.

---

## Comment 2

> Username: meshtag  
> Created_at: 2021-01-31 14:50:34 UTC  
> Updated_at: 2021-01-31 14:59:47 UTC  
> Url: https://github.com/boostorg/gil/pull/554#issuecomment-770393980  

I messed up with paths in my first attempt . Even after correcting all paths, it seems lcov is not able to find .gcno files in bin.v2 subdirectory, hence coverage.info file remains empty and system can't send coverage data to codecov. I am looking for a solution to this problem, please nudge me in the right direction .  
PS : It is quite probable that I made a beginner/noob's error since my understanding of how boost build works is still not crystal clear:|, trying to figure it out from the documentation .

---
