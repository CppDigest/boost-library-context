# #533 - PDB files are neither staged by `b2 stage` nor installed by `b2 install` [Closed]

> Username: pdimov  
> Created at: 2020-02-01 02:22:21 UTC  
> Updated at: 2022-09-02 12:03:25 UTC  
> Closed at: 2020-09-05 19:26:22 UTC  
> Url: https://github.com/boostorg/build/issues/533  

When this was pointed out to me, I at first thought that I had broken something with the move to `boost_install`. But it turns out that Boost 1.69, which predates my changes, doesn't install or stage PDB files either. This is presumably caused by package.install passing `<install-type>SHARED_LIB`, omitting `PDB`, and might be intentional.  
  
Do we remember why PDBs aren't staged or installed?  
  
@teeks99 any comments on the Windows packages? They don't seem to get the PDBs today. Bug or feature?

---

## Comment 1

> Username: mscottmueller  
> Created at: 2020-08-17 20:10:16 UTC  
> Updated at: 2020-09-16 03:04:18 UTC  
> Url: https://github.com/boostorg/build/issues/533#issuecomment-675087897  

I would very much like to see this addressed, or at least get a clue on how to fix it. It looks like the machinery is in place to stage the PDB files, but I'm not sure where to begin.

---

## Comment 2

> Username: pdimov  
> Created at: 2020-08-23 16:00:28 UTC  
> Url: https://github.com/boostorg/build/issues/533#issuecomment-678791592  

If you want to experiment with staging the PDB files, it can be enabled by adding `<install-type>PDB` to the following line: https://github.com/boostorg/boost_install/blob/cfa8b2b1eb9c8ba4d31d9333e8afb1fbd6994a14/boost-install.jam#L1224  
  
(That is, `<install-type>SHARED_LIB` becomes `<install-type>SHARED_LIB <install-type>PDB`.)  
  
This will only work for `b2 stage` and not `b2 install`, but the fix for `install` is similar, as long as we can decide whether we should do it or not. I was hoping that a Boost.Build maintainer would offer an opinion on that.

---

## Comment 3

> Username: grafikrobot  
> Created at: 2020-08-23 18:11:30 UTC  
> Url: https://github.com/boostorg/build/issues/533#issuecomment-678806313  

On Sun, Aug 23, 2020 at 11:00 AM Peter Dimov <notifications@github.com>  
wrote:  
  
> If you want to experiment with staging the PDB files, it can be enabled by  
> adding <install-type>PDB to the following line:  
> https://github.com/boostorg/boost_install/blob/cfa8b2b1eb9c8ba4d31d9333e8afb1fbd6994a14/boost-install.jam#L1224  
>  
> (That is, <install-type>SHARED_LIB becomes <install-type>SHARED_LIB  
> <install-type>PDB.)  
>  
> This will only work for b2 stage and not b2 install, but the fix for  
> install is similar, as long as we can decide whether we should do it or  
> not. I was hoping that a Boost.Build maintainer would offer an opinion on  
> that.  
>  
It's perfectly reasonable to install PDBs. Not sure how that has anything  
to do with B2 itself though.  
  
--   
-- René Ferdinand Rivera Morell  
-- Don't Assume Anything  -- No Supone Nada  
-- Robot Dreams - http://robot-dreams.net

---

## Comment 4

> Username: pdimov  
> Created at: 2020-08-23 19:58:13 UTC  
> Url: https://github.com/boostorg/build/issues/533#issuecomment-678817627  

`package.install` is part of B2 proper, and it doesn't install the PDB files. I'm using it in boost_install, so I inherit this behavior. I can of course "inline" it manually when installing, as I've done when staging, and install PDBs on `b2 install` without changing anything in B2 itself; the question was whether filtering out the PDBs in `package.install` was done on purpose.  
  
https://github.com/boostorg/build/blob/512ea1f0730b59ba6232709bf9da20fde5dce3ad/src/tools/package.jam#L234-L239  
  
There's a related question here about whether the correct DLL location on Windows is libdir, or bindir, as is done on Cygwin.

---

## Comment 5

> Username: pdimov  
> Created at: 2020-09-05 19:26:14 UTC  
> Url: https://github.com/boostorg/build/issues/533#issuecomment-687652113  

OK, I assume there's no interest in changing `package.install` to install PDBs, so I'll be handling this in `boost_install`: https://github.com/boostorg/boost_install/issues/45

---

## Comment 6

> Username: mscottmueller  
> Created at: 2020-09-05 20:37:01 UTC  
> Updated at: 2020-09-16 03:03:36 UTC  
> Url: https://github.com/boostorg/build/issues/533#issuecomment-687659926  

Hi Peter,  
  
Does that mean that b2 will be able to stage the PDB files along with the static libs?  
  
Best Regards,  
  
M. Scott Mueller

---

## Comment 7

> Username: pdimov  
> Created at: 2020-09-05 21:03:59 UTC  
> Url: https://github.com/boostorg/build/issues/533#issuecomment-687663002  

I don't think we support "compile PDBs" as CMake does. This only applies to DLLs.

---

## Comment 8

> Username: grafikrobot  
> Created at: 2020-09-05 21:43:21 UTC  
> Url: https://github.com/boostorg/build/issues/533#issuecomment-687666823  

> OK, I assume there's no interest in changing `package.install` to install PDBs, so I'll be handling this in `boost_install`: [boostorg/boost_install#45](https://github.com/boostorg/boost_install/issues/45)  
  
No. It means I haven't had time to look into doing it.

---

## Comment 9

> Username: mscottmueller  
> Created at: 2020-09-05 23:18:12 UTC  
> Updated at: 2020-09-16 03:08:36 UTC  
> Url: https://github.com/boostorg/build/issues/533#issuecomment-687674940  

The reason I care about this is that the PDBs from static libs (I’m using msvc) need to be found in order to have the debug information for those libraries included in the target binary. I want to use separate PDBs (/Zi) instead of building debug data within the static libs (/Z7) because of efficiency issues within the binary otherwise, and I can’t use shared DLLs because of deployment concerns in my environment.  
  
I’m also trying to use Binskim to detect whether or not we were compiling with /Qspectre flags. The PDB files exist within the build space, but I’m not sure how to get them into the staging destination. If I manually move them to the staging area, everything is found and detected properly. I’m just not versed in b2 well enough to know where to start with this.  
  
Best Regards,  
  
M. Scott Mueller

---

## Comment 10

> Username: pdimov  
> Created at: 2020-09-06 00:35:36 UTC  
> Url: https://github.com/boostorg/build/issues/533#issuecomment-687680886  

I was wrong, B2 does support "compile PDBs". You get them by using `debug-store=database`. However, there are some issues.  
  
When you do f.ex. `b2 --with-atomic debug-store=database`, the first snag is  
```  
libs\atomic\src\lock_pool.cpp: fatal error C1041: cannot open program database 'C:\boost-git\develop\bin.v2\libs\atomic\build\msvc-14.2\debug\address-model-64\debug-store-database\link-static\threading-multi\libboost_atomic-vc142-mt-gd-x64-1_75.pdb'; if multiple CL.EXE write to the same .PDB file, please use /FS  
```  
so we need to update our `msvc.jam` toolset to pass `/FS`. In the meantime, I tried to pass it directly via `b2 --with-atomic debug-store=database compileflags=-FS`, but this didn't work either (I filed #653 about it.) So one needs to use `b2 --with-atomic debug-store=database cflags=-FS cxxflags=-FS`.  
  
This works, and builds the PDB files, but doesn't stage them even with my patch https://github.com/boostorg/boost_install/commit/49c63cd756de9d32cc6271bd71556c3f7c39ff5c. The compile PDBs are not part of the library targets, so aren't installed/staged, and my B2 knowledge is insufficient to fix that. You can fish them out of the bin.v2 directories though.

---

## Comment 11

> Username: mscottmueller  
> Created at: 2020-09-06 00:37:24 UTC  
> Updated at: 2020-09-16 03:02:18 UTC  
> Url: https://github.com/boostorg/build/issues/533#issuecomment-687681024  

Yep, that’s where I ended up!  Thanks for looking!  
  
Best Regards,  
  
M. Scott Mueller

---

## Comment 12

> Username: drywolf  
> Created at: 2022-09-02 12:02:32 UTC  
> Updated at: 2022-09-02 12:03:25 UTC  
> Url: https://github.com/boostorg/build/issues/533#issuecomment-1235417595  

I have also run into this same issue.  
  
I want to build static boost libraries + compile PDBs and install the PDBs alongside their .lib files.  
So far I have also not found any way that works out of the box with `b2`,  
and when trying to automatically `fish the PDBs out of the bin.v2 directories` via a script, this was also super fragile and would break at any chance.  
  
Is this supported by boost in 2022 and I am barking up the wrong tree ? 🤞
