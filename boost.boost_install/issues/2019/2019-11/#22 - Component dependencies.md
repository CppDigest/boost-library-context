# #22 - Component dependencies [Closed]

> Username: mathstuf  
> Created at: 2019-11-05 14:53:24 UTC  
> Updated at: 2020-05-06 19:25:40 UTC  
> Closed at: 2020-05-06 19:25:40 UTC  
> Url: https://github.com/boostorg/boost_install/issues/22  

Similar to #18, but more tractable since it is internal dependencies only.  
  
```cmake  
find_package(Boost CONFIG COMPONENTS thread)  
```  
  
ends up with an "unknown target Boost::system" message. The transitive closure of the requested components should be made available to the requesting package because otherwise every package needs to embed this information internally. `FindBoost.cmake` handled the dependency tree at least, so this behavior for compatibility across the gap would be really helpful in that way too.

---

## Comment 1

> Username: pdimov  
> Created at: 2019-11-05 15:44:45 UTC  
> Url: https://github.com/boostorg/boost_install/issues/22#issuecomment-549880143  

BoostConfig does bring in dependencies. https://github.com/boostorg/boost_install/blob/develop/test/thread/CMakeLists.txt passes, so there must be something else going on. What are the exact steps that lead to the failure?

---

## Comment 2

> Username: pdimov  
> Created at: 2019-11-05 15:54:04 UTC  
> Url: https://github.com/boostorg/boost_install/issues/22#issuecomment-549883928  

If you're trying to use `Boost::system` further down in the `CMakeLists.txt` file, the problem is caused by the fact that Boost.Thread no longer indirectly depends on Boost.System via Boost.Chrono, due to this change: https://github.com/boostorg/chrono/commit/5fd1761c8395f052a7c0fe9e1143404d2dd21550  
  
BoostConfig gets its dependency info from the Jamfiles, so if they don't declare a dependency, it won't either.  
  
FindBoost looks at autolink instead, so it's possible for the two to get out of sync, although in this case this shouldn't be the case; Boost.Chrono should no longer autolink to Boost.System either.

---

## Comment 3

> Username: mathstuf  
> Created at: 2019-11-05 15:55:36 UTC  
> Url: https://github.com/boostorg/boost_install/issues/22#issuecomment-549884572  

Hmm. I'll have to play around in the environment I found it in. The machine is busy at the moment, but I should get time by the end of the week.

---

## Comment 4

> Username: pdimov  
> Created at: 2019-11-05 15:59:04 UTC  
> Url: https://github.com/boostorg/boost_install/issues/22#issuecomment-549886078  

FindBoost declares a Thread dependency on System in 1.68: https://github.com/Kitware/CMake/blob/b089396f8e60c7a4a9e5fbece8484e7e77fd503a/Modules/FindBoost.cmake#L1111  
  
and doesn't in 1.69: https://github.com/Kitware/CMake/blob/b089396f8e60c7a4a9e5fbece8484e7e77fd503a/Modules/FindBoost.cmake#L1125

---

## Comment 5

> Username: mathstuf  
> Created at: 2019-11-05 16:03:33 UTC  
> Url: https://github.com/boostorg/boost_install/issues/22#issuecomment-549888082  

Ah. This might be an issue with the third party project claiming too much and then forcing `system` on me because it uses `Boost::system`. I'll investigate that angle.
