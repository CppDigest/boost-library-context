# #286 fix uninitilized variable in framework_init_observer_t [Closed]

> Username: K-os  
> Created at: 2020-10-07 10:37:19 UTC  
> Updated at: 2022-03-02 07:48:33 UTC  
> Closed at: 2022-03-01 22:37:52 UTC  
> Url: https://github.com/boostorg/test/pull/286  

fixes: #272

---

## Comment 1

> Username: vakatov  
> Created_at: 2021-12-01 03:26:56 UTC  
> Url: https://github.com/boostorg/test/pull/286#issuecomment-983251244  

Did this fix fall through the cracks? It fixes problem for us but... still not in the releases. Without the fix, we are stuck with 1.72.0 (or, internally, with patched Boost 1.73.0+).

---

## Comment 2

> Username: K-os  
> Created_at: 2022-02-21 09:50:34 UTC  
> Url: https://github.com/boostorg/test/pull/286#issuecomment-1046674372  

I think this project is dead.  
  
The number of open issues is growing and the only ones closed in the last year were closed by the reporters themselves. Also no PRs were merged for over a year.

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2022-02-21 09:51:50 UTC  
> Url: https://github.com/boostorg/test/pull/286#issuecomment-1046675670  

The project is not dead.

---

## Comment 4

> Username: vakatov  
> Created_at: 2022-02-22 23:16:30 UTC  
> Updated_at: 2022-02-22 23:18:30 UTC  
> Url: https://github.com/boostorg/test/pull/286#issuecomment-1048301794  

Are there plans to deal with this bug? It was identified 1.5 years ago, and it clearly affects more than one user.  
  
FWIW, I find it hard to believe that the (extremely trivial) two PRs to fix this bug can be blamed for any regressions.

---

## Comment 5

> Username: raffienficiaud  
> Created_at: 2022-02-26 22:07:30 UTC  
> Url: https://github.com/boostorg/test/pull/286#issuecomment-1052688488  

Yes, the CI was dead since I moved from my previous job and now I fixed it. Travis and Appveyor are just not working.  
I will fix the current issues on develop and run the CI on this one. This should be done in the next couple of days.

---

## Comment 6

> Username: raffienficiaud  
> Created_at: 2022-03-01 22:37:52 UTC  
> Url: https://github.com/boostorg/test/pull/286#issuecomment-1055928881  

Fixed through #328 rev 194a69e

---

## Comment 7

> Username: raffienficiaud  
> Created_at: 2022-03-01 22:38:18 UTC  
> Url: https://github.com/boostorg/test/pull/286#issuecomment-1055929210  

Thank you for the patch!

---

## Comment 8

> Username: vakatov  
> Created_at: 2022-03-01 22:58:50 UTC  
> Url: https://github.com/boostorg/test/pull/286#issuecomment-1055944368  

Great, thanks much @K-os and @raffienficiaud!

---

## Comment 9

> Username: K-os  
> Created_at: 2022-03-02 07:48:32 UTC  
> Url: https://github.com/boostorg/test/pull/286#issuecomment-1056487451  

Good to see progress in this project. Thanks for maintaining it.

---
