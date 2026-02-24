# #677 - Unable to load B2: could not find build system on Gentoo since 1.74 [Closed]

> Username: rascalDan  
> Created at: 2020-11-28 15:43:05 UTC  
> Updated at: 2021-01-11 20:00:55 UTC  
> Closed at: 2020-12-22 19:49:36 UTC  
> Url: https://github.com/boostorg/build/issues/677  

I opened a bug with the Gentoo team, but they admit a lack of full understanding (see https://bugs.gentoo.org/738034) but copying here for clarity:  
  
Install completes successfully, all appears fine.  
  
However, running bjam (in a manner that worked in previous versions) results in a failure.  
  
Reproducible: Always  
  
Steps to Reproduce:  
1. `emerge boost-build`  
2. `bjam`  
Actual Results:    
```  
$ bjam  
Unable to load B2: could not find build system.  
-----------------------------------------------  
/usr/share/boost-build/boost-build.jam attempted to load the build system by invoking  
  
   'boost-build src/kernel ;'  
  
but we were unable to find 'bootstrap.jam' in the specified directory or in BOOST_BUILD_PATH:  
    /usr/share/boost-build/src/kernel  
Please consult the documentation at 'https://boostorg.github.io/build/'.  
  
Unable to load B2  
-----------------  
'/usr/share/boost-build/boost-build.jam' was found by searching from /home/randomdan up to the root.  
  
However, it failed to call the 'boost-build' rule to indicate the location of the build system.  
  
Please consult the documentation at 'https://boostorg.github.io/build/'.  
```  
  
Expected Results:    
```  
$ bjam  
...found 641 targets...  
etc  
```  
  
I've looked upstream, could be related a few things... but maybe this one https://github.com/boostorg/build/issues/622, specifically the change to startup.cpp.  
  
That being said, setting `BOOST_BUILD_PATH=/usr/share/boost-build/` also works, as does `BOOST_BUILD_PATH=/usr/share/boost-build/kernel/` as both these paths contain bootstrap.jam, one referring to the other. Doesn't feel right that a environment variable be required for default behaviour.  
  
However, as per the error message, `/usr/share/boost-build/boost-build.jam` refers to `src/kernel` which does not exist once installed, it's just `kernel`. Changing this to just `kernel` is another fix. This can't be patched with a user patch as prepare runs in `tools/src` and this file is in `tools`.  
  
Not sure which of these is the real problem/fix or if this is an upstream problem or a Gentoo packaging problem.  
  
I've tried applying the patch from #622 (commit c6b74d0683c7209fb7cf6f18130b4371672ce21d) but this doesn't change anything.  
  
Happy to provide more details/information to anyone who might be able help identify where the problem is and fix it, just need to know what.  
  
My gut feeling is that the Gentoo packaging is quirky as there is no `src` directory in `/usr/share/boost-build` like there is in other distros (`build`, `kernel`, `options`, `tools`, `util` are all directly in `boost-build` folder, not `boost-build/src`) and that it previous worked by accident or some other default behaviour in the Jam version that was not ported to the C++ version... but I don't have a full understanding of it either.  
  
Appreciate any pointers / assistance on this, thanks.

---

## Comment 1

> Username: perfect7gentleman  
> Created at: 2020-12-20 16:52:15 UTC  
> Updated at: 2020-12-20 16:53:33 UTC  
> Url: https://github.com/boostorg/build/issues/677#issuecomment-748632108  

[look here](https://gitlab.com/Perfect_Gentleman/PG_Overlay/-/blob/master/dev-util/boost-build/boost-build-1.75.0_pre.ebuild#L58)

---

## Comment 2

> Username: grafikrobot  
> Created at: 2020-12-22 19:49:36 UTC  
> Updated at: 2020-12-26 22:34:20 UTC  
> Url: https://github.com/boostorg/build/issues/677#issuecomment-749743971  

This is fixed in develop, it was a duo of issues. And will be in the next release of B2 (very soon). And hence will be in the 1.76 Boost release.

---

## Comment 3

> Username: sledgehammer999  
> Created at: 2020-12-26 21:58:56 UTC  
> Url: https://github.com/boostorg/build/issues/677#issuecomment-751400179  

>And hence will be in the 1.75 Boost release.  
  
FYI, I get the same error on Windows with boost 1.75. Did you mean that it will be in the 1.76 release?

---

## Comment 4

> Username: grafikrobot  
> Created at: 2020-12-26 22:34:39 UTC  
> Url: https://github.com/boostorg/build/issues/677#issuecomment-751402905  

Yes, I meant the 1.76 release. Sorry about the typo.

---

## Comment 5

> Username: rascalDan  
> Created at: 2021-01-11 20:00:55 UTC  
> Url: https://github.com/boostorg/build/issues/677#issuecomment-758189341  

Something still not right here, I've built bjam from head of this repo, the error message has changed:  
```  
$ bjam -n  
Unable to load B2: could not find 'boost-build.jam'  
---------------------------------------------------  
Attempted search from '/home/randomdan/dev/library' up to the root at '/usr/bin/bjam'  
Please consult the documentation at 'https://boostorg.github.io/build/'.  
```  
`/home/randomdan/dev/library` contains `Jamroot.jam`.  
The previous fix of changing `/usr/share/boost-build/boost-build.jam` to refer to just `kernel` not `src/kernel` no longer works.
