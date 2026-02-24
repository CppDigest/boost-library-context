# #795 CML Cleanup [Closed]

> Username: cmazakas  
> Created at: 2023-12-04 20:01:34 UTC  
> Updated at: 2023-12-04 21:11:12 UTC  
> Closed at: 2023-12-04 21:11:12 UTC  
> Url: https://github.com/boostorg/url/pull/795  

URL is problematic to use a subdirectory for both users and fellow devs.  
  
This PR brings URL in line with how Boost libraries should behave both in terms of being used as a subdirectory of the superproject and being used as a subdirectory without the entire superproject.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-12-04 20:24:28 UTC  
> Updated_at: 2023-12-04 20:26:28 UTC  
> Url: https://github.com/boostorg/url/pull/795#issuecomment-1839416815  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/795?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#795](https://app.codecov.io/gh/boostorg/url/pull/795?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (eb49fbb) into [develop](https://app.codecov.io/gh/boostorg/url/commit/d99b89ac160fca4ad3a6d8b425db1a4a5ef0638f?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (d99b89a) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/795/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/795?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #795   +/-   ##  
========================================  
  Coverage    97.27%   97.27%             
========================================  
  Files          156      156             
  Lines         8565     8565             
========================================  
  Hits          8332     8332             
  Misses         233      233             
```  
  
  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/795?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/795?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [d99b89a...eb49fbb](https://app.codecov.io/gh/boostorg/url/pull/795?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: alandefreitas  
> Created_at: 2023-12-04 20:24:29 UTC  
> Url: https://github.com/boostorg/url/pull/795#issuecomment-1839416845  

> URL is problematic to use a subdirectory for both users and fellow devs.  
  
Do you mean problematic for another project to include it as a subdirectory? I think Vinnie asked me to remove this as a feature at some point in the past but I'm OK with it.  
  
Or do you mean problematic for Boost to include it as a subdirectory? This is not a problem at all. It's something we support and it's extensively tested.  
  
> This PR brings URL in line with how Boost libraries should behave both in terms of being used as a subdirectory of the superproject and being used as a subdirectory without the entire superproject.  
  
I don't think that's possible. The default CMakeLists.txt generated for other boost libraries does not support "being used as a subdirectory without the entire superproject" unless it happens to work by accident. This is a feature I have asked for and ended up implementing (as both BOOST_SRC_DIR and find_package) because it has many use cases, but IIRC Peter always argued for a single path that would only work as a subdirectory of the superproject like other Boost libraries do.

---

## Comment 3

> Username: cmazakas  
> Created_at: 2023-12-04 20:37:42 UTC  
> Url: https://github.com/boostorg/url/pull/795#issuecomment-1839436691  

> Do you mean problematic for another project to include it as a subdirectory? I think Vinnie asked me to remove this as a feature at some point in the past but I'm OK with it.  
  
Yes, this is what I mean.  
  
More importantly, consider this:  
```cmake  
if(BOOST_CI_INSTALL_TEST)  
    find_package(Boost CONFIG REQUIRED COMPONENTS url)  
else()  
    set(BOOST_URL_BUILD_TESTS OFF CACHE BOOL "Build the tests." FORCE)  
    add_subdirectory(../.. boostorg/url)  
    add_subdirectory(../../../assert/ boost/assert)  
endif()  
```  
  
This absolutely breaks because of how URL tries to auto-include the superproject:  
```bash  
CMake Error at /home/exbigboss/cpp/boost-root/libs/assert/CMakeLists.txt:11 (add_library):  
  add_library cannot create ALIAS target "Boost::assert" because another  
  target with the same name already exists.  
```  
  
The subdirectory tests for Boost libs are supposed to be of a form like this: https://github.com/boostorg/smart_ptr/blob/develop/test/cmake_subdir_test/CMakeLists.txt  
  
>  The default CMakeLists.txt generated for other boost libraries does not support "being used as a subdirectory without the entire superproject" unless it happens to work by accident.   
  
Users just need to `add_subdirectory()` everything generated from the `boostdep --brief` report.

---

## Comment 4

> Username: cmazakas  
> Created_at: 2023-12-04 20:40:47 UTC  
> Updated_at: 2023-12-04 20:46:55 UTC  
> Url: https://github.com/boostorg/url/pull/795#issuecomment-1839440734  

More importantly, if I'm writing my own CMake subdir test here, URL is the outlier that breaks my build when I use it like this:  
https://github.com/cmazakas/buffers/blob/a0d07d842dff7619494f7af7d5c00ed84c0ee562/test/cmake_test/CMakeLists.txt  
  
URL should be brought into line with how its peers behave so that this kind of stuff isn't catastrophic.  
  
Edit:  
  
Peter's documented this kind of workflow here: https://github.com/boostorg/cmake#using-an-individual-boost-library-with-add_subdirectory

---

## Review 5 [Commented]

> Username: alandefreitas  
> Created_at: 2023-12-04 21:00:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/795#pullrequestreview-1763350721  

> This PR brings URL in line with how Boost libraries should behave both in terms of being used as a subdirectory of the superproject and being used as a subdirectory without the entire superproject.  
  
I have the impression that this PR does the opposite of that.   
  
Boost.URL's CMakeLists.txt supports other use cases beyond simple Boost libraries so that it can be used as an independent project between releases and the same logic is useful for libraries that are still being proposed. We had to take a lot of care and spent a lot of time to find a design that doesn't conflict with the usual usage expected from the superproject.  
  
"Vanilla" Boost libraries are the opposite. They have very simple CMakeLists.txt and always assume they are in `<superproject>/libs`. For instance, have a look a files generated by boostdep.   
  
So instead of going in the direction of simple Boost libraries, this PR is attempting to support for more things in Boost.URL. I'm usually OK with supporting more things, but I think the semantics here are breaking things on which we put a lot of thought in the past or relying on bad CMake semantics on the part of the user.  
  
I don't know the motivation for these changes, but I guess most of the problem goes away if the intended library consumer just calls the scripts with common CMake semantics:  
  
```cmake  
# Parent projects (probably the super-project) including url and inheriting defaults  
add_subdirectory(url)  
```  
  
```cmake  
# Some project adding including Boost.URL as a subproject but somewhat pretending this is an external dependency  
set(BOOST_URL_BUILD_TESTS  OFF CACHE BOOL "" FORCE)  
add_subdirectory(url) # or BOOST_URL_SRC_DIR  
```  
  
Unless the intention is to use the "mini tests library". In that case, nothing is really supposed to work because Boost.URL defines no semantics to share it. We usually just copy and paste these files. Even if some other project is sure the Boost.URL sources are available somewhere, it just needs to put them in a target.

📁 CMakeLists.txt

```diff
  33 | #-------------------------------------------------
  34 |- option(BOOST_URL_BUILD_TESTS "Build boost::url tests" ${BUILD_TESTING})
  34 |+ if(BOOST_URL_IS_ROOT OR BOOST_SUPERPROJECT_VERSION)
```

> Username: alandefreitas  
> Created_at: 2023-12-04 20:32:47 UTC  
> Updated_at: 2023-12-04 21:00:34 UTC  
> Url: https://github.com/boostorg/url/pull/795#discussion_r1414457910  

This logic is not needed. The very reason for the existence `BOOST_URL_BUILD_TESTS` separate from `BUILD_TESTING` is to make this distinction.   
  
The semantics of `BUILD_TESTING` of least surprise is "build tests for everything I include" (regardless of whether the one doing the including is the Boost superproject). This is the expected semantics we like it or not. I understand we often don't like it because we often think of FetchContent and add_subdirectory equivalents as mini package managers but it's not what's designed to do and CMake doesn't have the semantics to express some add_subdirectories as "special".  
  
In this context, that's why people define variables separate from `BUILD_TESTING`, so other projects can still define it and disable tests for subdirectories that are considered "special" in that they are being treated as dependencies more than part of the project.  
  
```cmake  
set(BOOST_URL_BUILD_TESTS  OFF CACHE BOOL "" FORCE)  
add_subdirectory(${BOOST_URL_SRC_DIR})  
```  
  
That or whatever logic relates to the logic for tests of parent project. CMake doesn't have to know if the parent or child projects have a "special" relationship (like the superproject and boost.url do) neither provides a way to express that. Code trying to be to "smart" about it to guess what the user wants on some edge cases is only likely to create surprises.

---

📁 CMakeLists.txt

```diff
  84 | #-------------------------------------------------
  80 |- if (NOT BOOST_SUPERPROJECT_VERSION)
  85 |+ if (BOOST_URL_IS_ROOT)
```

> Username: alandefreitas  
> Created_at: 2023-12-04 20:44:00 UTC  
> Updated_at: 2023-12-04 21:00:34 UTC  
> Url: https://github.com/boostorg/url/pull/795#discussion_r1414471426  

This is what we had a long time ago and it caused problems. When BOOST_URL_IS_ROOT is true, it doesn't mean we want to take this "standalone" path because this is only circumstancially true when `url` is in `libs`: in this case we still want to add "../.." excluding self to use the Boost scripts.


📁 test/cmake_test/CMakeLists.txt

```diff
  15 |     find_package(Boost CONFIG REQUIRED COMPONENTS url)
  16 | else()
  17 |-     set(BOOST_URL_BUILD_TESTS OFF CACHE BOOL "Build the tests." FORCE)
```

> Username: alandefreitas  
> Created_at: 2023-12-04 20:46:25 UTC  
> Updated_at: 2023-12-04 21:00:34 UTC  
> Url: https://github.com/boostorg/url/pull/795#discussion_r1414473635  

That goes back to add_subdirectory not being a package manager. If we just do `add_subdirectory`, the CMake semantics is that this subdirectory is a subproject and not an external project or a sub-project emulating an external project. In any case, this is just to avoid a case that's not even likely to happen because we shouldn't set `BUILD_TESTING` for the `cmake_test`. It's just here in case some other script happens to set it.

---

📁 test/cmake_test/CMakeLists.txt

```diff
  17 |     add_subdirectory(../.. boostorg/url)
  18 |+ 
  19 |+     set(deps
```

> Username: alandefreitas  
> Created_at: 2023-12-04 20:48:26 UTC  
> Updated_at: 2023-12-04 21:00:34 UTC  
> Url: https://github.com/boostorg/url/pull/795#discussion_r1414476391  

None of that should be needed. This is a byproduct of breaking CMakeLists.txt. If Boost.URL is in `<superproject>/libs`, it should be using Boost.CMake functionality to include whatever dependencies it needs.


---
