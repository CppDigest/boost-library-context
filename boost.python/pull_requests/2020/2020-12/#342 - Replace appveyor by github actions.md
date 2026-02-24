# #342 Replace appveyor by github actions. [Merged]

> Username: stefanseefeld  
> Created at: 2020-12-26 20:00:23 UTC  
> Updated at: 2021-04-15 22:58:37 UTC  
> Merged at: 2021-01-04 05:21:08 UTC  
> Closed at: 2021-01-04 05:21:08 UTC  
> Url: https://github.com/boostorg/python/pull/342  



---

## Review 1 [Commented]

> Username: teeks99  
> Created_at: 2020-12-26 22:56:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/python/pull/342#pullrequestreview-558894675  

📁 .github/workflows/test-windows.yml

```diff
  10 |+       matrix:
  11 |+         python: [python3]
  12 |+ 
```

> Username: teeks99  
> Created_at: 2020-12-26 22:48:37 UTC  
> Updated_at: 2021-01-04 04:33:55 UTC  
> Url: https://github.com/boostorg/python/pull/342#discussion_r549039368  

You could add matrix variables for the different msvc versions that you want to run with. I'm not sure how it works with faber, but with b2 you could put:  
  
    cxxtoolset: [msvc-14.0, msvc-14.1, msvc-14.2]  
      
Then at invocation time  
  
    b2 toolset=${{ matrix.cxxtoolset }}

> Username: teeks99  
> Created_at: 2020-12-26 22:51:03 UTC  
> Updated_at: 2021-01-04 04:33:55 UTC  
> Url: https://github.com/boostorg/python/pull/342#discussion_r549039514  

Hang on, it I'm not sure that is valid with `windows-latest`. I was thinking I saw multiple visual studio versions installed on that image, but [it looks like there is only one](https://github.com/actions/virtual-environments/blob/main/images/win/Windows2019-Readme.md#visual-studio-enterprise-2019).   
  
That one does have the toolsets for 14.0, 14.1 and 14.2 all installed, but I've never had luck building boost across the different toolsets from one Visual Studio install, I've always needed more dependencies from visual studio than just the toolset.

> Username: stefanseefeld  
> Created_at: 2020-12-26 23:10:12 UTC  
> Updated_at: 2021-01-04 04:33:55 UTC  
> Url: https://github.com/boostorg/python/pull/342#discussion_r549040929  

The `msvc` module in faber will discover all installed versions, then instantiate a compiler for each, which can be selected on the command-line by specifying the version via `cxx.version=...` (see https://github.com/boostorg/python/blob/develop/.appveyor.yml#L89)

---

📁 .github/workflows/test-windows.yml

```diff
  20 |+       with:
  21 |+         #vcpkgArguments: boost:x64-windows
  22 |+         vcpkgGitCommitId: '8a9a97315aefb3f8bc5d81bf66ca0025938b9c91'
```

> Username: teeks99  
> Created_at: 2020-12-26 22:55:49 UTC  
> Updated_at: 2021-01-04 04:33:55 UTC  
> Url: https://github.com/boostorg/python/pull/342#discussion_r549039847  

Instead of using the static copy in vcpkg, wouldn't it make more sense to clone master/develop and use that? I'm definitely not a vcpkg expert, so I may be missing some of the benefit here.

> Username: stefanseefeld  
> Created_at: 2020-12-26 23:12:27 UTC  
> Updated_at: 2021-01-04 04:33:55 UTC  
> Url: https://github.com/boostorg/python/pull/342#discussion_r549041051  

I assume by "static copy" you are referring to the fact that I'm fixing the commit ID, yes ? I tried to use the `lukka/run-vcpkg` action without specifying that, which prompted an error, instructing me to add the required git commit ID.  
  
I don't really know what I'm doing here, just stumbling in the dark...

> Username: teeks99  
> Created_at: 2020-12-27 00:55:36 UTC  
> Updated_at: 2021-01-04 04:33:55 UTC  
> Url: https://github.com/boostorg/python/pull/342#discussion_r549048385  

By "static copy" I meant "always the same version of boost" e.g. 1.75.0. Since this is the development/master version of boost::python that is going into the next release, wouldn't it make sense to test with the version of boost that is also going into the next release?

> Username: stefanseefeld  
> Created_at: 2020-12-27 04:22:08 UTC  
> Updated_at: 2021-01-04 04:33:55 UTC  
> Url: https://github.com/boostorg/python/pull/342#discussion_r549063100  

Not necessarily: If Boost.Python's code changes, it will be mostly its use of the Python C API, rather than its use of the rest of Boost. So the only reason to test against Boost develop would be to test that Boost itself doesn't break compatibility.  
As far as Boost.Python is concerned, using a "static" version of Boost as prerequisite seems entirely reasonably and a good way to optimize CI builds.


---

## Review 2 [Commented]

> Username: teeks99  
> Created_at: 2020-12-26 23:06:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/python/pull/342#pullrequestreview-558895172  

📁 .github/workflows/test-windows.yml

```diff
  13 |+     steps:
  14 |+     - uses: actions/checkout@v2
  15 |+     - uses: ilammy/msvc-dev-cmd@v1
```

> Username: teeks99  
> Created_at: 2020-12-26 23:06:52 UTC  
> Updated_at: 2021-01-04 04:33:55 UTC  
> Url: https://github.com/boostorg/python/pull/342#discussion_r549040684  

Just to clarify, this just sets some environmental variables so the msvc tools are directly available. Does Faber need this to have happened? B2 didn't use this method (though it could fall back to it).   
  
This might be the way to go for this use case. It also looks like you can set 64 vs. 32 bit builds here...though we'd also have to get the correct python installed. The [image docs](https://github.com/actions/virtual-environments/blob/main/images/win/Windows2019-Readme.md#python) say both x86 and x64 are on there, but I'm not clear where they are.

> Username: stefanseefeld  
> Created_at: 2020-12-26 23:14:34 UTC  
> Updated_at: 2021-01-04 04:33:55 UTC  
> Url: https://github.com/boostorg/python/pull/342#discussion_r549041207  

I see. No, Faber only needs to be able to *find* MSVC, using `vswhere`. So as long as MSVC and `vswhere` are installed, all is fine. If `ilammy/msvc-dev-cmd` only sets some variables, it's of no use here indeed and I will remove it.


---
