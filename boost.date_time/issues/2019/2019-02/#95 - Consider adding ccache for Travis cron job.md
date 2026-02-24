# #95 - Consider adding ccache for Travis cron job [Closed]

> Username: Kojoley  
> Created at: 2019-02-08 20:06:42 UTC  
> Updated at: 2022-03-01 16:19:40 UTC  
> Closed at: 2022-03-01 16:19:40 UTC  
> Url: https://github.com/boostorg/date_time/issues/95  

Travis is configured to build Boost.DateTime every week on master and develop, the build takes 7 hours. Please consider configuring ccache.  
  
*Update: Previously overlooked and thought it is configured for every day job*

---

## Comment 1

> Username: jeking3  
> Created at: 2019-02-10 15:04:47 UTC  
> Url: https://github.com/boostorg/date_time/issues/95#issuecomment-462140007  

Every day?!  I thought I set it monthly.

---

## Comment 2

> Username: jeking3  
> Created at: 2019-02-10 15:05:42 UTC  
> Url: https://github.com/boostorg/date_time/issues/95#issuecomment-462140074  

It runs once per week, not daily.

---

## Comment 3

> Username: jeking3  
> Created at: 2019-02-14 22:31:31 UTC  
> Url: https://github.com/boostorg/date_time/issues/95#issuecomment-463828900  

Curious - how do you propose ccache could be used to optimize the build jobs when the point of the CI build is to test different compilers and language levels?  Would it be better to pare back the number of jobs we're running?  Do we still need to be running as many cxxstd=98,03 jobs at this point, for example?

---

## Comment 4

> Username: Kojoley  
> Created at: 2019-02-14 23:08:10 UTC  
> Url: https://github.com/boostorg/date_time/issues/95#issuecomment-463839637  

ccache automatically version objects cache depend invocation flags, additionally you can split Travis cache with `CACHE_NAME` env variable, just add  
```  
  - export CACHE_NAME=$TRAVIS_OS_NAME-$TOOLSET-$CXXSTD  
```

---

## Comment 5

> Username: jeking3  
> Created at: 2019-02-15 00:19:05 UTC  
> Url: https://github.com/boostorg/date_time/issues/95#issuecomment-463856697  

I see, so you're proposing to store away the ccache database between builds.  I've used it this way before, but usually with only one compiler and one language level.  We can give it a try.  It would be an interesting addition to put in [Boost-CI](https://github.com/boostorg/boost-ci).

---

## Comment 6

> Username: JeffGarland  
> Created at: 2020-03-04 18:05:36 UTC  
> Url: https://github.com/boostorg/date_time/issues/95#issuecomment-594713784  

I'm also curious if this would improve the response of the CI on a pull request.  If you look at pull #126 you will see that there's been a series of commits to fix regressions, etc.  It takes basically a day to get updated information since the 2 CI tools run serially and so many compiler/variations.

---

## Comment 7

> Username: Kojoley  
> Created at: 2020-04-06 09:38:57 UTC  
> Url: https://github.com/boostorg/date_time/issues/95#issuecomment-609686533  

@JeffGarland I would have added ccache to the repository back than, but it uses boost-ci and I am not interested in screwing into bash and write all checks for all OSes, etc.

---

## Comment 8

> Username: jeking3  
> Created at: 2022-03-01 16:19:40 UTC  
> Url: https://github.com/boostorg/date_time/issues/95#issuecomment-1055615980  

We're not using Travis any more; boost-ci uses ccache, so closing.
