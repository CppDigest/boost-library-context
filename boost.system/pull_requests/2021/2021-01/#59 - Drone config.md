# #59 Drone config [Merged]

> Username: sdarwin  
> Created at: 2021-01-12 17:11:32 UTC  
> Updated at: 2022-02-06 20:08:07 UTC  
> Merged at: 2022-02-06 20:08:07 UTC  
> Closed at: 2022-02-06 20:08:07 UTC  
> Url: https://github.com/boostorg/system/pull/59  

Drone is a continuous integration framework similar to Travis CI. [The C++ Alliance](https://cppalliance.org/) is offering a hosted Drone server for Boost libraries. Please refer to https://github.com/CPPAlliance/drone-ci for more information and instructions.

---

## Comment 1

> Username: pdimov  
> Created_at: 2021-09-07 21:54:55 UTC  
> Url: https://github.com/boostorg/system/pull/59#issuecomment-914653622  

Since we've completely lost our OS X coverage, Drone could be valuable in providing it, but as-is, this PR doesn't seem of use to me. I'd rather have a self-contained file that doesn't include anything from another repo, and putting all the .sh scripts into one doesn't seem necessary.

---

## Comment 2

> Username: sdarwin  
> Created_at: 2021-09-07 23:01:09 UTC  
> Url: https://github.com/boostorg/system/pull/59#issuecomment-914680217  

> Since we've completely lost our OS X coverage    
  
Github Actions does offer at least some OS X coverage.     
```  
os: macos-10.15  
```  
  
> I'd rather have a self-contained file  
  
although hosting sections of the config at https://github.com/boostorg/boost-ci/ was not originally my idea, it has turned out to be very convenient.  Those parts of the config correspond to things that Travis or Github Actions are providing behind-the-scenes.  It makes sense for certain steps to be standardized and maintained at the CI level, instead of within each individual job.    When using Travis, there is a lot of 'dependence' they are setting up their machines correctly, and parsing your config correctly.   The situation with Drone is analogous, in this case 'dependence' on the boost-ci repo.  
  
I am not sure, if you are saying that by copying 400 lines of code from boostorg/boost-ci into your local drone.sh file, and into every repo's drone.sh file, it would be an improvement.   I believe it would be less manageable.  
  
I will close the PR for now.  It's still open to discussion.

---

## Comment 3

> Username: pdimov  
> Created_at: 2021-09-07 23:09:55 UTC  
> Url: https://github.com/boostorg/system/pull/59#issuecomment-914683752  

Suppose my Drone config only has OS X jobs. I only need a copy of the `osx_cxx` function, and then I wouldn't even need most of it, as it's boilerplate that my jobs won't need. It calls `osx-cxx-install.sh` but that's empty so I don't need it either.

---

## Comment 4

> Username: pdimov  
> Created_at: 2021-09-07 23:15:43 UTC  
> Url: https://github.com/boostorg/system/pull/59#issuecomment-914686000  

I'll keep this open as a reminder.

---

## Comment 5

> Username: sdarwin  
> Created_at: 2021-09-08 00:40:21 UTC  
> Url: https://github.com/boostorg/system/pull/59#issuecomment-914733929  

You could reduce boilerplate by removing the call to functions.star, and copying in osx_cxx.  
  
However, a section of osx_cxx:  
  
```  
    if not osx_version:  
        if xcode_version[0:2] in [ "12","11","10"]:  
            osx_version="catalina"  
        elif xcode_version[0:1] in [ "9","8","7","6"]:  
            osx_version="highsierra"  
  else:  
    osx_version="catalina"  
```  
  
Is routing jobs to certain machines at the datacenter, and might change in the future.  So having the function administered centrally will be more convenient.

---
