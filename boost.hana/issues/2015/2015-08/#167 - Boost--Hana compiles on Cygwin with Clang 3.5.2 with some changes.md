# #167 - Boost::Hana compiles on Cygwin with Clang 3.5.2 with some changes [Closed]

> Username: m-j-w  
> Created at: 2015-08-30 20:41:32 UTC  
> Updated at: 2015-09-03 22:44:44 UTC  
> Closed at: 2015-09-03 22:44:44 UTC  
> Url: https://github.com/boostorg/hana/issues/167  

Hi Louis,  
after some tweaking, Boost::Hana successfully compiles most of the tests and examples on Cygwin.  
There were two major issues:   
1) Clang 3.5.2 currently is not able to compile with exceptions, thus requires the flap -fno-exceptions. Accordingly, all tests and examples using exceptions fail.  
2) Clang also builds with the C++ standard library provided by g++ 4.9.3. Hence, the type traits std::is_trivially_*, the struct aligned_union_t, and some examples using std::to_string fail.  
  
Unfortunately ruby 1.8 is too old in the default cygwin package, so benchmarks are not working.  
Other than that, everything seems fine. If you want, I can provide some patches preferably via email; not yet that much experience with github pull requests...  
  
Cheers, Markus.  
  
Cygwin 64 on Windows 7  
clang version 3.5.2 (tags/RELEASE_352/final)  
Target: x86_64-unknown-windows-cygnus, Thread model: posix  
g++ (GCC) 4.9.3 providing libstdc++6 (version 4.9.3-1) and supposedly used for linking  
cmake version 3.3.1

---

## Comment 1

> Username: ldionne  
> Created at: 2015-08-30 20:57:35 UTC  
> Url: https://github.com/boostorg/hana/issues/167#issuecomment-136190263  

Glad to see someone trying to port to other platforms! Patches will be appreciated; you can send them by email if you prefer, or you can submit a pull request as explained in the [contributing guidelines](https://github.com/ldionne/hana/blob/c3046d0fafe5b7309ed48970e580bf412f264e3b/CONTRIBUTING.md).

---

## Comment 2

> Username: m-j-w  
> Created at: 2015-08-30 22:22:14 UTC  
> Url: https://github.com/boostorg/hana/issues/167#issuecomment-136208712  

Okay, I'll try the pull request. Which branch should I fork from ? I see, you're currently making large changes in develop and feature/document-datatypes. And would you prefer a feature/cygwin or bugfix/cygwin ? I'll then add individual commits for the mentioned issues.

---

## Comment 3

> Username: ldionne  
> Created at: 2015-08-31 01:53:25 UTC  
> Url: https://github.com/boostorg/hana/issues/167#issuecomment-136232215  

Just fork `develop`, and I'll rebase my other pull requests as needed. As for the name of the branch, I don't care too much. `bugfix/cygwin` seems to make more sense since we're fixing something that should "already work", but I'm really fine with either.

---

## Comment 4

> Username: ldionne  
> Created at: 2015-09-03 22:44:44 UTC  
> Url: https://github.com/boostorg/hana/issues/167#issuecomment-137593967  

I will close this in favour of pull requests #168 and #172.
