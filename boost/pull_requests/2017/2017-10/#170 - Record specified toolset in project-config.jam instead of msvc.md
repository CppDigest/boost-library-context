# #170 Record specified toolset in project-config.jam instead of msvc [Merged]

> Username: pdimov  
> Created at: 2017-10-30 16:00:17 UTC  
> Updated at: 2017-12-01 16:10:24 UTC  
> Merged at: 2017-12-01 16:10:15 UTC  
> Closed at: 2017-12-01 16:10:15 UTC  
> Url: https://github.com/boostorg/boost/pull/170  

This allows `bootstrap gcc` to put `using gcc ;` in `project-config.jam` instead of `using msvc ;`.  
  
Supersedes #5.

---

## Comment 1

> Username: pdimov  
> Created_at: 2017-10-30 16:09:01 UTC  
> Url: https://github.com/boostorg/boost/pull/170#issuecomment-340494957  

Someone with access to boostorg's Appveyor account should look into why it doesn't test pull requests. :-)

---

## Comment 2

> Username: pdimov  
> Created_at: 2017-10-31 00:10:07 UTC  
> Url: https://github.com/boostorg/boost/pull/170#issuecomment-340621521  

On second thought, this is not correct. `bootstrap vc14` will put `using vc14 ;` in `project-config.jam` and we don't want that.

---

## Comment 3

> Username: pdimov  
> Created_at: 2017-11-01 16:30:24 UTC  
> Url: https://github.com/boostorg/boost/pull/170#issuecomment-341160028  

@vprus @grafikrobot Please review.

---

## Comment 4

> Username: grafikrobot  
> Created_at: 2017-11-01 16:43:59 UTC  
> Url: https://github.com/boostorg/boost/pull/170#issuecomment-341164144  

I have no comment.. As, strange as it is, I didn't write this file.

---
