# #53 - Windows target platform _WIN32_WINNT=0x0A00 (Win10) FAMILY=PC (most restrictive) build failures (UWP support) [Closed]

> Username: jeking3  
> Created at: 2017-09-15 14:46:33 UTC  
> Updated at: 2018-11-11 00:34:39 UTC  
> Closed at: 2017-10-07 11:30:43 UTC  
> Url: https://github.com/boostorg/winapi/issues/53  

Winapi needs updates to support UWP Windows Store application development.  
Build job: https://ci.appveyor.com/project/jeking3/winapi/build/job/mfhg74xyt5jrt2tk  
There are too many failures to list them here individually.

---

## Comment 1

> Username: jeking3  
> Created at: 2017-09-17 13:34:48 UTC  
> Url: https://github.com/boostorg/winapi/issues/53#issuecomment-330044615  

Hopefully I'll have a PR for this later today - I found a way to make uwp partition checks universal for all SDKs.

---

## Comment 2

> Username: jeking3  
> Created at: 2017-09-19 03:29:48 UTC  
> Url: https://github.com/boostorg/winapi/issues/53#issuecomment-330418524  

Turning out to be a fair bit more work than expected.  Lots of fun with the preprocessor.
