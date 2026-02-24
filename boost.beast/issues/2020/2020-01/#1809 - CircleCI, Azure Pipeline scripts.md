# #1809 - CircleCI, Azure Pipeline scripts [Closed]

> Username: vinniefalco  
> Created at: 2020-01-20 14:06:04 UTC  
> Updated at: 2025-04-27 17:04:59 UTC  
> Closed at: 2025-04-27 17:04:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1809  

We can test more configurations if we add CircleCI and Azure Pipeline test scripts

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-01-27 10:48:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1809#issuecomment-578692340  

commencing

---

## Comment 2

> Username: stale[bot]  
> Created at: 2020-02-27 05:33:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1809#issuecomment-591790505  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2022-09-24 05:44:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1809#issuecomment-1256872265  

@vinniefalco is this superseded by drone?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-09-25 00:33:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1809#issuecomment-1257090764  

Azure kind of sucks and so does Circle. Practically speaking only Drone and GitHub Actions are workable. We should try to divide the workload between Drone and GHA so that the turnarounds are a bit faster. Drone can have the latest and oldest of each, plus some in the middle, and half of the remaining sent to GHA.
