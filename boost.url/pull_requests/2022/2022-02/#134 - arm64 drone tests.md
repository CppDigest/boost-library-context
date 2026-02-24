# #134 arm64 drone tests [Closed]

> Username: alandefreitas  
> Created at: 2022-02-28 20:23:27 UTC  
> Updated at: 2022-03-14 15:55:50 UTC  
> Closed at: 2022-03-12 03:08:41 UTC  
> Url: https://github.com/boostorg/url/pull/134  

close #111

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-11 22:54:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/134#pullrequestreview-907869645  

📁 .drone.star

```diff
  16 |   return [
  17 |-   linux_cxx("COMMENT=codecov.io LCOV_BRANCH_COVERAGE=0 B2_ Job 2", "g++-8", packages="g++-8", buildscript="drone", buildtype="codecov", image=linuxglobalimage, environment={'COMMENT': 'codecov.io', 'LCOV_BRANCH_COVERAGE': '0', 'B2_CXXSTD': '11', 'B2_TOOLSET': 'gcc-8', 'B2_DEFINES': 'BOOST_NO_STRESS_TEST=1', 'DRONE_JOB_UUID': 'da4b9237ba', "CODECOV_TOKEN": {"from_secret": "codecov_token"}}, globalenv=globalenv),
  17 |+   linux_cxx("clang-12 arm64", "clang++-12", packages="clang-12 libstdc++-9-dev", llvm_os="focal", llvm_ver="12", buildscript="drone", buildtype="boost", image="cppalliance/droneubuntu2004:multiarch", environment={'B2_TOOLSET': 'clang-12', 'B2_CXXSTD': '11,14,17,20', 'DRONE_JOB_UUID': '472b07ba03'}, arch="arm64", globalenv=globalenv),
```

> Username: vinniefalco  
> Created_at: 2022-03-11 22:54:52 UTC  
> Url: https://github.com/boostorg/url/pull/134#discussion_r825177629  

codecov needs to come first, then doc, then latest gcc, then latest clang, then valgrind/ubsan, and then finally the rest in however order you want

> Username: alandefreitas  
> Created_at: 2022-03-12 00:33:59 UTC  
> Updated_at: 2022-03-12 00:34:00 UTC  
> Url: https://github.com/boostorg/url/pull/134#discussion_r825211664  

https://github.com/alandefreitas/url/blob/feature/arm64/.drone.star


---
