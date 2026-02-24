# #679 - Usage of outdated bzip2 1.0.6 and zlib 1.2.8 for windows binaries [Closed]

> Username: chcg  
> Created at: 2020-11-30 19:22:40 UTC  
> Updated at: 2020-11-30 20:03:50 UTC  
> Closed at: 2020-11-30 20:03:50 UTC  
> Url: https://github.com/boostorg/build/issues/679  

Unclear if this is the right place to report the finding, but at least there are some references here in the repo for bzip2 and zlib.  
  
For the prebuild windows binaries e.g. from https://dl.bintray.com/boostorg/release/1.74.0/binaries/DEPENDENCY_VERSIONS.txt  
and upcoming https://dl.bintray.com/boostorg/beta/1.75.0.beta1/binaries/DEPENDENCY_VERSIONS.txt  
if I understand correct the build was done with:  
  
zlib: 1.2.8  
bzip2: 1.0.6  
  
Both of them are outdated and have CVE findings, see:  
  
bzip2 1.0.8:  
https://sourceware.org/git/?p=bzip2.git;a=blob_plain;f=CHANGES;hb=6a8690fc8d26c815e798c588f796eabe9d684cf0  
CVE-2016-3189, CVE-2019-12900  
  
zlib 1.2.11  
https://github.com/madler/zlib/releases/tag/v1.2.11  
CVE-2016-9841(https://github.com/madler/zlib/commit/9aaec95e82117c1cb0f9624264c3618fc380cecb#commitcomment-30737571)

---

## Comment 1

> Username: grafikrobot  
> Created at: 2020-11-30 19:45:09 UTC  
> Url: https://github.com/boostorg/build/issues/679#issuecomment-736001107  

This is a general Boost problem, i.e. not B2. Specifically a problem for @teeks99 who puts together those binaries.

---

## Comment 2

> Username: chcg  
> Created at: 2020-11-30 19:55:35 UTC  
> Url: https://github.com/boostorg/build/issues/679#issuecomment-736007305  

@teeks99 Would https://github.com/teeks99/boost-build/issues be the right place for this?

---

## Comment 3

> Username: teeks99  
> Created at: 2020-11-30 19:59:17 UTC  
> Url: https://github.com/boostorg/build/issues/679#issuecomment-736009392  

Yeah, I'll try to get it in before 1.75.

---

## Comment 4

> Username: teeks99  
> Created at: 2020-11-30 20:00:38 UTC  
> Url: https://github.com/boostorg/build/issues/679#issuecomment-736010052  

Actually, this is the correct one: https://github.com/teeks99/boost-release-windows
