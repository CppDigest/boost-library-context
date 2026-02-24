# #33 Clang and ICC on Windows fixes [Merged]

> Username: Kojoley  
> Created at: 2019-11-22 10:34:27 UTC  
> Updated at: 2019-11-22 17:46:18 UTC  
> Merged at: 2019-11-22 15:45:21 UTC  
> Closed at: 2019-11-22 15:45:21 UTC  
> Url: https://github.com/boostorg/dll/pull/33  

There is still have an issue on Clang, because ctti on it returns a space after a comma that delimits template arguments while demangler does not (`boost::variant<double, int>` vs `boost::variant<double,int>`).  
  
Also had to fix CI issues. Some of them are not mandatory, but `embed-manifest=off` part required to Clang on VS2019 image (for some reason Boost.Build does not find `mt.exe`), `dist: trusty` required because Travis switched default image to `xenial`.

---

## Comment 1

> Username: Kojoley  
> Created_at: 2019-11-22 10:59:02 UTC  
> Url: https://github.com/boostorg/dll/pull/33#issuecomment-557488597  

I do not see Appveyor here, it seems to be not enabled for the repository? Here are results from the PR source slag https://ci.appveyor.com/project/Kojoley/dll/builds/29038837

---

## Comment 2

> Username: apolukhin  
> Created_at: 2019-11-22 15:45:38 UTC  
> Url: https://github.com/boostorg/dll/pull/33#issuecomment-557583524  

Many many thanks for the fixes!

---

## Comment 3

> Username: apolukhin  
> Created_at: 2019-11-22 17:05:29 UTC  
> Url: https://github.com/boostorg/dll/pull/33#issuecomment-557613141  

> Are you sure this works correctly? I was getting empty `BOOST_BRANCH` variable. https://travis-ci.com/Kojoley/dll/jobs/259254217  
  
That's strange. I have that line in all the CI and it works pretty well https://github.com/boostorg/variant/blob/c401edba70c9f3668dae55ee1cfa5d9b2c6fc2fc/.travis.yml#L21

---

## Comment 4

> Username: Kojoley  
> Created_at: 2019-11-22 17:41:15 UTC  
> Url: https://github.com/boostorg/dll/pull/33#issuecomment-557625459  

Probably the repositories have define `BOOST_BRANCH` in Travis UI? In the fork it fails even on develop branch https://travis-ci.com/Kojoley/dll/jobs/259574420

---
