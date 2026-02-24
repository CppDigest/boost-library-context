# #340 Fix required CMake version [Merged]

> Username: Flamefire  
> Created at: 2025-10-01 08:20:38 UTC  
> Updated at: 2025-10-01 14:52:46 UTC  
> Merged at: 2025-10-01 14:38:45 UTC  
> Closed at: 2025-10-01 14:38:45 UTC  
> Url: https://github.com/boostorg/filesystem/pull/340  

`cxx_std_11` requires CMake 3.8

---

## Comment 1

> Username: Lastique  
> Created_at: 2025-10-01 10:14:15 UTC  
> Url: https://github.com/boostorg/filesystem/pull/340#issuecomment-3355648525  

`cxx_std_11` is not used in Boost.Filesystem. Why is this needed?

---

## Comment 2

> Username: Flamefire  
> Created_at: 2025-10-01 10:43:05 UTC  
> Url: https://github.com/boostorg/filesystem/pull/340#issuecomment-3355746037  

It should be used because the library requires C++11, see the Jamfile: https://github.com/boostorg/filesystem/blob/ce7a835436f5de0cca2966420068a2596f50a75d/build/Jamfile.v2#L132-L142  
  
It doesn't fail without it though because this gets added by a dependency already which is what causes the error in the dependency **and** Boost.Filesystem.

---

## Comment 3

> Username: Lastique  
> Created_at: 2025-10-01 11:16:09 UTC  
> Url: https://github.com/boostorg/filesystem/pull/340#issuecomment-3355850836  

> It should be used because the library requires C++11, see the Jamfile  
  
This is reflected in CMake here:  
  
https://github.com/boostorg/filesystem/blob/ce7a835436f5de0cca2966420068a2596f50a75d/CMakeLists.txt#L78-L89  
  
I believe, the dependency that requires it is Boost.Atomic, but that got recently fixed. Do you still have the issue after that fix was applied?

---

## Comment 4

> Username: Flamefire  
> Created_at: 2025-10-01 11:30:07 UTC  
> Url: https://github.com/boostorg/filesystem/pull/340#issuecomment-3355892491  

I missed that, sorry.  
  
> I believe, the dependency that requires it is Boost.Atomic, but that got recently fixed. Do you still have the issue after that fix was applied?  
  
Do you mean https://github.com/boostorg/atomic/pull/78 ? That means to build Boost.Atomic you (declare to) need CMake 3.8, and as FileSystem requires Atomic it needs CMake 3.8 too.  
  
The error here shows because `cxx_std_11` is added to boost_filesystem by boost_atomic so CMake shows the error for both targets.  
  
You can also simplify the list to just `cxx_std_11` as you'll get that anyway.

---

## Comment 5

> Username: Lastique  
> Created_at: 2025-10-01 14:38:37 UTC  
> Url: https://github.com/boostorg/filesystem/pull/340#issuecomment-3356735448  

The thing is if CMake < 3.8 is used to build Boost.Filesystem, it should fail at Boost.Atomic, as expected. There's nothing in Boost.Filesystem itself that requires 3.8, so it should not be an error to specify 3.5. Projects should not be required to track the max min required CMake version across their dependency tree, it's CMake's job to figure that out. I consider this a CMake issue.  
  
Anyway, raising the min version is not really a problem, if it works around the issue. Thanks again.

---

## Comment 6

> Username: Flamefire  
> Created_at: 2025-10-01 14:52:46 UTC  
> Url: https://github.com/boostorg/filesystem/pull/340#issuecomment-3356818130  

> The thing is if CMake < 3.8 is used to build Boost.Filesystem, it should fail at Boost.Atomic, as expected.   
  
With https://github.com/boostorg/atomic/pull/78 it now does. It didn't before and I simply opened PRs in each repo where CMake showed the unknown feature w/o checking if that feature was used by the library itself or a dependency as I wasn't aware it would verify features so late the error shows up in dependents too.  
  
With "as FileSystem requires Atomic it needs CMake 3.8 too." I didn't want to say that you need to declare that you need CMake 3.8 as done in this PR but that you effectively do require CMake 3.8 because otherwise configuring Filesystem will fail while configuring the Atomic dependency.    
So there is no real change by changing the minimum version here.  
  
But: As you effectively need CMake 3.8 anyway you can replace that long list with `cxx_std_11` and directly declare the 3.8 requirement as done in this PR.

---
