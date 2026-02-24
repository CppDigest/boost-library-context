# #184 GHA: Use codecov action to upload coverage [Merged]

> Username: Flamefire  
> Created at: 2024-04-17 11:08:11 UTC  
> Updated at: 2024-04-18 06:10:36 UTC  
> Merged at: 2024-04-18 06:08:34 UTC  
> Closed at: 2024-04-18 06:08:35 UTC  
> Url: https://github.com/boostorg/charconv/pull/184  

This might workaround https://github.com/boostorg/boost-ci/issues/230  
  
I had experienced similar issues and found them to occur less with the action instead of the script. You might want to set up a `CODECOV_TOKEN` as a secret in this repository as I assume the error is caused by rate limits. That token avoids that at least when the PR is coming from this repo or the branch got merged.

---

## Comment 1

> Username: mborland  
> Created_at: 2024-04-17 11:12:18 UTC  
> Url: https://github.com/boostorg/charconv/pull/184#issuecomment-2061013838  

> You might want to set up a `CODECOV_TOKEN` as a secret in this repository...  
  
I have it in there; I double checked the value a few days ago to make sure it was updated when the repo was moved into boostorg.

---

## Comment 2

> Username: Flamefire  
> Created_at: 2024-04-17 13:13:37 UTC  
> Url: https://github.com/boostorg/charconv/pull/184#issuecomment-2061234227  

> `CODECOV_TOKEN`  
  
It wasn't used by the Boost.CI script. Changed that just now.

---

## Comment 3

> Username: mborland  
> Created_at: 2024-04-18 06:08:26 UTC  
> Url: https://github.com/boostorg/charconv/pull/184#issuecomment-2063072011  

Thanks for this. Didn't get the results notification because drone errored, but they are viewable on the website.

---
