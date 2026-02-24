# #251 Added functional dependency to master [Merged]

> Username: denzor200  
> Created at: 2022-07-04 10:26:13 UTC  
> Updated at: 2022-07-15 01:59:32 UTC  
> Merged at: 2022-07-04 10:31:01 UTC  
> Closed at: 2022-07-04 10:31:01 UTC  
> Url: https://github.com/boostorg/fusion/pull/251  



---

## Comment 1

> Username: Lastique  
> Created_at: 2022-07-14 16:15:04 UTC  
> Url: https://github.com/boostorg/fusion/pull/251#issuecomment-1184634294  

Please revert this as it breaks CMake CI in Boost.Filesystem:  
  
https://ci.appveyor.com/project/Lastique/filesystem/builds/44164520/job/3a21ltf2vn0mks9j#L209  
  
This PR is supposed to add a dependency for #240 but it was not merged to master.  
  
PS: Never ever merge PRs directly to master, unless they synchronize with develop.

---

## Comment 2

> Username: Flamefire  
> Created_at: 2022-07-14 20:34:49 UTC  
> Updated_at: 2022-07-14 20:42:03 UTC  
> Url: https://github.com/boostorg/fusion/pull/251#issuecomment-1184872680  

Yes this breaks the CMake CI in many libs because this PR introduces a dependency to `Boost::functional` but as `master` doesn't include #240 there actually is no dependency of the code on Functional.   
  
So what happens is that `depinst.py` scans the headers and does NOT find Functional so it will not get downloaded. Then the CMake script requires `Boost::functional` but that isn't there and the generation fails.  
  
As @pdimov wrote in https://github.com/boostorg/fusion/pull/240#issuecomment-1159213885 :   
  
> `boostdep --cmake fusion > CMakeLists.txt`  
  
And of course:  
  
> PS: Never ever merge PRs directly to master, unless they synchronize with develop.  
  
You should only merge the whole develop to master because that is what master is meant to be: The stable branch after the changes have been tested in develop. So you cannot cherry-pick single commits to master directly because they might miss other commits which that one depends on as this example shows.  
  
PS: https://github.com/boostorg/boost-ci/ has maintained CI configs. The Github actions CI contains ccache-based caching and also CMake builds which would have likely detected this. I'd suggest to replace yours with that one: https://github.com/boostorg/boost-ci/blob/master/.github/workflows/ci.yml  
  
You likely don't want/need the coverage and coverity jobs, so just remove those rows: https://github.com/boostorg/boost-ci/blob/dee2f7a6077ddb85ac8a0129f6a0999fa8aedd98/.github/workflows/ci.yml#L61-L62 https://github.com/boostorg/boost-ci/blob/dee2f7a6077ddb85ac8a0129f6a0999fa8aedd98/.github/workflows/ci.yml#L95-L96  
  
Cmake builds are here: https://github.com/boostorg/boost-ci/blob/dee2f7a6077ddb85ac8a0129f6a0999fa8aedd98/.github/workflows/ci.yml#L353

---

## Comment 3

> Username: djowel  
> Created_at: 2022-07-15 01:59:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/251#issuecomment-1185091132  

Reverted.

---
