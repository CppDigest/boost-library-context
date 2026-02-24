# #333 Reorder CI targets [Closed]

> Username: alandefreitas  
> Created at: 2022-08-04 18:12:10 UTC  
> Updated at: 2022-08-08 14:38:49 UTC  
> Closed at: 2022-08-04 22:47:09 UTC  
> Url: https://github.com/boostorg/url/pull/333  

fix #331, fix #332

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-04 18:16:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/333#pullrequestreview-1062400912  

📁 .drone.star

```diff
  17 |+   # Coverage
  18 |+   linux_cxx("Coverage", "g++-8", packages="g++-8", buildscript="drone", buildtype="codecov", image=linuxglobalimage, environment={'COMMENT': 'codecov.io', 'LCOV_BRANCH_COVERAGE': '0', 'B2_CXXSTD': '11', 'B2_TOOLSET': 'gcc-8', 'B2_DEFINES': 'BOOST_NO_STRESS_TEST=1', 'DRONE_JOB_UUID': 'da4b9237ba', "CODECOV_TOKEN": {"from_secret": "codecov_token"}}, globalenv=globalenv),
  19 |+   linux_cxx("Coverity Scan", "g++", packages="", buildscript="drone", buildtype="coverity", image=linuxglobalimage, environment={'COMMENT': 'Coverity Scan', 'B2_TOOLSET': 'clang', 'DRONE_JOB_UUID': '4d134bc072'}, globalenv=globalenv),
```

> Username: vinniefalco  
> Created_at: 2022-08-04 18:16:11 UTC  
> Url: https://github.com/boostorg/url/pull/333#discussion_r938113104  

The hell with Coverity, they want us to pay. Put them at the bottom, commented out.


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-04 18:16:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/333#pullrequestreview-1062401343  

📁 .drone.star

```diff
  15 | def main(ctx):
  16 |   return [
  17 |+   # Coverage
```

> Username: vinniefalco  
> Created_at: 2022-08-04 18:16:33 UTC  
> Url: https://github.com/boostorg/url/pull/333#discussion_r938113390  

Please paste the chart we made, at the top of this function  
```  
  # Priorities: (no 2a, no betas)  
  #  
  # coverage  
  # latest gcc: 11,14,17,20  
  # latest clang: 11,14,17,20  
  # oldest gcc: 4.8  
  # oldest clang: 3.8  
  # valgrind  
  # asan  
  # tsan  
  # ubsan  
  # docs  
  # arm64  
  # s390x  
  # (...the rest)  
```

> Username: alandefreitas  
> Created_at: 2022-08-04 18:18:54 UTC  
> Url: https://github.com/boostorg/url/pull/333#discussion_r938115501  

I interleaved it with the jobs. But I'll put it there.


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-04 18:19:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/333#pullrequestreview-1062404760  

📁 .drone.star

```diff
  22 |+   # 5. Documentation
  23 |+   # 6. Other platforms
  24 |+   # 7. The rest
```

> Username: vinniefalco  
> Created_at: 2022-08-04 18:19:27 UTC  
> Url: https://github.com/boostorg/url/pull/333#discussion_r938115938  

No!!! This is not what we wrote. Again:  
```  
  # Priorities: (no 2a, no betas)  
  #  
  # coverage  
  # latest gcc: 11,14,17,20  
  # latest clang: 11,14,17,20  
  # oldest gcc: 4.8  
  # oldest clang: 3.8  
  # valgrind  
  # asan  
  # tsan  
  # ubsan  
  # docs  
  # arm64  
  # s390x  
  # (...the rest)  
```

> Username: alandefreitas  
> Created_at: 2022-08-04 18:20:01 UTC  
> Url: https://github.com/boostorg/url/pull/333#discussion_r938116340  

Oh... you really want the ugly version... ok


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-04 18:22:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/333#pullrequestreview-1062408601  

📁 .drone.star

```diff
  37 |+   linux_cxx("GCC 12: C++17", "g++-12", packages="g++-12", buildscript="drone", buildtype="boost", image="cppalliance/droneubuntu2204:1", environment={'B2_TOOLSET': 'gcc-12', 'B2_CXXFLAGS': '-funsigned-char', 'B2_CXXSTD': '17,20'}, globalenv=globalenv),
  38 |+   linux_cxx("Clang 14: C++11, 14, 17, 20", "clang++-14", packages="clang-14 libstdc++-10-dev", llvm_os="jammy", llvm_ver="14", buildscript="drone", buildtype="boost", image="cppalliance/droneubuntu2204:1", environment={'B2_TOOLSET': 'clang-14', 'B2_CXXSTD': '11,14,17,20'}, globalenv=globalenv),
  39 |+   windows_cxx("MSVC 14.3: C++17, 20", "", image="cppalliance/dronevs2022", buildtype="boost", buildscript="drone", environment={"B2_TOOLSET": "msvc-14.3", "B2_CXXSTD": "17,20"},globalenv=globalenv),
```

> Username: vinniefalco  
> Created_at: 2022-08-04 18:22:52 UTC  
> Url: https://github.com/boostorg/url/pull/333#discussion_r938118460  

It isn't "Latest compilers" it is  
```  
  # latest gcc: 11,14,17,20  
  # latest clang: 11,14,17,20  
```  
  
and there's no MSVC, look at the chart (this is why we pasted it)


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-04 18:23:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/333#pullrequestreview-1062409476  

📁 .drone.star

```diff
  25 |+   # asan
  26 |+   # tsan
  27 |+   # ubsan
```

> Username: vinniefalco  
> Created_at: 2022-08-04 18:23:39 UTC  
> Url: https://github.com/boostorg/url/pull/333#discussion_r938119068  

edit this list to reflect that we moved valgrind as the last sanitizer


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-04 18:24:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/333#pullrequestreview-1062409894  

📁 .drone.star

```diff
  26 |+   # tsan
  27 |+   # ubsan
  28 |+   # docs
```

> Username: vinniefalco  
> Created_at: 2022-08-04 18:24:01 UTC  
> Url: https://github.com/boostorg/url/pull/333#discussion_r938119347  

Edit this list to reflect that we put the docs last

> Username: alandefreitas  
> Created_at: 2022-08-04 18:27:08 UTC  
> Updated_at: 2022-08-04 18:27:09 UTC  
> Url: https://github.com/boostorg/url/pull/333#discussion_r938121897  

We didn't. docs is right after valgrind.


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-04 18:29:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/333#pullrequestreview-1062415421  

📁 .drone.star

```diff
  29 |+   # arm64
  30 |+   # s390x
  31 |+   # (...the rest)
```

> Username: vinniefalco  
> Created_at: 2022-08-04 18:29:03 UTC  
> Url: https://github.com/boostorg/url/pull/333#discussion_r938123348  

add the cmake/install targets to this list, and add your name in the copyright


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-04 18:29:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/333#pullrequestreview-1062415862  

📁 .drone.star

```diff
  59 |+   linux_cxx("CMake Install", "g++", packages="", buildscript="drone", buildtype="cmake1", image="cppalliance/droneubuntu1804:1", environment={'CMAKE_INSTALL_TEST': '1', 'DRONE_JOB_UUID': 'b6589fc6ab'}, globalenv=globalenv),
  60 |+ 
  61 |+   # arm64 (unsigned char)
```

> Username: vinniefalco  
> Created_at: 2022-08-04 18:29:25 UTC  
> Updated_at: 2022-08-04 18:29:26 UTC  
> Url: https://github.com/boostorg/url/pull/333#discussion_r938123665  

Put a big horizontal line in a comment just before this line


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-04 18:34:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/333#pullrequestreview-1062421389  

📁 .drone.star

```diff
  29 |+   # docs
  30 |+   # cmake superproject
  31 |+   # cmake install
```

> Username: vinniefalco  
> Created_at: 2022-08-04 18:34:19 UTC  
> Url: https://github.com/boostorg/url/pull/333#discussion_r938127679  

cmake and docs should be at the end of this list, under arm/s390x (and update the drone lines)

> Username: alandefreitas  
> Created_at: 2022-08-04 18:43:22 UTC  
> Url: https://github.com/boostorg/url/pull/333#discussion_r938134954  

Changed it  
  
(Even though docs fail orders of magnitude more often than arm/s390x and cmake fail orders of magnitude more often than docs)


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-04 19:30:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/333#pullrequestreview-1062490948  

📁 .drone.star

```diff
  53 |+   linux_cxx("Valgrind", "clang++-6.0", packages="clang-6.0 libc6-dbg libc++-dev libstdc++-8-dev", llvm_os="bionic", llvm_ver="6.0", buildscript="drone", buildtype="valgrind", image=linuxglobalimage, environment={'COMMENT': 'valgrind', 'B2_TOOLSET': 'clang-6.0', 'B2_CXXSTD': '11,14', 'B2_DEFINES': 'BOOST_NO_STRESS_TEST=1', 'B2_VARIANT': 'debug', 'B2_TESTFLAGS': 'testing.launcher=valgrind', 'VALGRIND_OPTS': '--error-exitcode=1', 'DRONE_JOB_UUID': '1b64538924'}, globalenv=globalenv),
  54 |+ 
  55 |+   # ------------------------------------------------------------------
```

> Username: vinniefalco  
> Created_at: 2022-08-04 19:30:02 UTC  
> Updated_at: 2022-08-04 19:30:03 UTC  
> Url: https://github.com/boostorg/url/pull/333#discussion_r938177921  

Nope. This line is supposed to separate the items in the priority list, from "the rest"


---
