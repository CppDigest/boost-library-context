# #50 - Reduce build matrix [Closed]

> Username: Flamefire  
> Created at: 2019-12-15 12:42:20 UTC  
> Updated at: 2019-12-19 08:22:58 UTC  
> Closed at: 2019-12-19 08:22:58 UTC  
> Url: https://github.com/boostorg/nowide/issues/50  

There are currently 31 jobs on travis, 2 on OSX & 29 on Linux. However this is a (mostly) windows only library. So I'd cut down on the number of jobs because IMO most of them are not useful (they test the std:: functions) and just increase PR test times  
  
There will be a test on Linux testing the custom `filebuf`, which is IMO the trickiest part, so linux jobs are still useful. But I'd just have:  
- g++-4.4  
- g++-9  
- g++-9 + UBSAN + gold (@pdimov any reason why gold is used here?)  
- clang++-3.3  
- clang++-9 + UBSAN  
- clang++-libc++ + UBSAN  
- OSX + ubsan  
  
These are the lowest and latest compiler versions which should be enough.  
  
@pdimov Any advice/opinion here? If you think the other jobs should be kept, how about putting them in another stage and enabling that conditionally for "not PR". That would allow full tests for merged commits but faster turnaround for PRs. But as written I don't see the usefulness of those other jobs for this library and the number of Jobs on travis is limited.     
BTW: `sudo: false` is no longer required and can be removed from your template: https://config.travis-ci.com/ref/sudo. I'd also use `TRAVIS_COMPILER` instead of redundantly setting `COMPILER` and `compiler`. Any reason you didn't do that?

---

## Comment 1

> Username: pdimov  
> Created at: 2019-12-15 14:25:39 UTC  
> Url: https://github.com/boostorg/nowide/issues/50#issuecomment-565814379  

`gold` is needed to avoid an error. `TRAVIS_COMPILER` isn't used only because it didn't exist when I originally wrote the .yml files and I haven't updated all of them, so it depends on which I happen to copy as a starting point.  
  
You should probably leave the default g++ as well (it's gcc 5 on Xenial.)  
  
There are sometimes differences in warnings so if you are going to use -Werror it might be worth retaining more versions, or it might not be.
