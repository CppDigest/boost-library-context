# #14 - Boost configs broken by cross-prefix symlinks [Open]

> Username: Flamefire  
> Created at: 2019-10-16 14:01:36 UTC  
> Updated at: 2020-11-11 17:17:19 UTC  
> Url: https://github.com/boostorg/boost_install/issues/14  

On Arch /lib64 and /usr/lib64 are symlinks to /usr/lib, so a Boost installed into /usr but found via /lib64 will fail to set the correct paths for e.g. the include dir as reported in https://gitlab.kitware.com/cmake/cmake/issues/19841 (It will try to use `/include` instead of `/usr/include` where the former does not exist)  
  
A solution would look similar to https://gitlab.kitware.com/cmake/cmake/blob/v3.15.4/Source/cmExportInstallFileGenerator.cxx#L201-219 as mentioned by Brad King at https://gitlab.kitware.com/cmake/cmake/issues/19841#note_640123

---

## Comment 1

> Username: pdimov  
> Created at: 2019-10-25 13:30:42 UTC  
> Url: https://github.com/boostorg/boost_install/issues/14#issuecomment-546354661  

Should be fixed on develop using Brad King's suggestion.

---

## Comment 2

> Username: AdamMajer  
> Created at: 2019-11-19 16:26:57 UTC  
> Url: https://github.com/boostorg/boost_install/issues/14#issuecomment-555589171  

Theis solution actually breaks things. For distributions, Boost is installed to a prefix other than root. For example, I use  
  
```specfile  
./b2 -d+2 -q --user-config=./user-config-py3.jam \  
    --build-type=minimal --build-dir=./python3-build \  
    --python-buildid=py3 \  
    --prefix=%{buildroot}%{package_prefix} --exec-prefix=%{buildroot}%{package_bindir} \  
    --libdir=%{buildroot}%{package_libdir} --includedir=%{buildroot}%{package_includedir} \  
    --stagedir=./python3-stage %{?_smp_mflags} \  
    $PY_LIBRARIES_FLAGS \  
    link=shared runtime-link=shared install  
```  
  
So now the installed cmake files contain the %{buildroot} part of the prefix (it's normally something like `/home/abuild/BUILD`, which is only used for creating a package. A nicer way of doing this would be to have DESTDIR env variable used, like with autotools, but I'm not sure what is the best solution for Boost as now these directories are being used by more than just Boost.Build to copy files into.

---

## Comment 3

> Username: pdimov  
> Created at: 2019-11-19 16:40:58 UTC  
> Url: https://github.com/boostorg/boost_install/issues/14#issuecomment-555595690  

It shouldn't break things in this configuration. The logic checks whether the realpath of the computed CMake directory is equivalent to the one used when building (`/home/abuild/BUILD` in this case). If it isn't - and it won't be - everything remains the same.

---

## Comment 4

> Username: AdamMajer  
> Created at: 2019-11-19 16:46:06 UTC  
> Url: https://github.com/boostorg/boost_install/issues/14#issuecomment-555597964  

Maybe I'm reading this wrong, but it's also comparing apples to tires. The bug was about `/lib64` -> `/usr/lib`. But if the package is built with a prefix under a user directory and then installed into `/usr/lib` by the package, the comparison will fail as the initial install directory at Boost build's time is not related to `/usr/lib`

---

## Comment 5

> Username: pdimov  
> Created at: 2019-11-19 16:58:51 UTC  
> Url: https://github.com/boostorg/boost_install/issues/14#issuecomment-555604053  

Yes, it will fail. The fix can only kick in if the package is built in the same directory it's later installed into.

---

## Comment 6

> Username: dennisklein  
> Created at: 2019-11-19 19:35:07 UTC  
> Url: https://github.com/boostorg/boost_install/issues/14#issuecomment-555673627  

> A nicer way of doing this would be to have DESTDIR env variable used, like with autotools  
  
If you find a DESTDIR-like workflow for `b2`, I would appreciate if you share it. I only know of `b2 --prefix=/usr --stagedir=/tmp/pkg stage`, which I thought would be the `DESTDIR` equivalent, but it only produces the `lib` folder (maybe `include/boost` is promised to be relocatable?!). In the case of the discussed generated cmake file here, `b2 stage` does not help either, because it bakes in the stagedir now instead of the prefix (contrary to what I was expecting).

---

## Comment 7

> Username: pdimov  
> Created at: 2019-11-19 19:47:15 UTC  
> Url: https://github.com/boostorg/boost_install/issues/14#issuecomment-555679114  

There is no DESTDIR equivalent in b2, or if there is, I don't know about it. `b2 stage` is not a staged install in the DESTDIR sense; it just builds Boost into the stagedir, without doing anything else. It does not copy any headers and is basically a workflow for building Boost in place.  
  
This never came up before I implemented CMake config file generation because the directories were never embedded anywhere.

---

## Comment 8

> Username: dennisklein  
> Created at: 2019-11-19 21:15:02 UTC  
> Updated at: 2019-11-19 21:15:37 UTC  
> Url: https://github.com/boostorg/boost_install/issues/14#issuecomment-555718068  

> This never came up before I implemented CMake config file generation because the directories were never embedded anywhere.  
  
I suspect the same, all files installed by Boost just happened to be relocatable before it seems. I checked several packages (Arch, Fedora and Debian) and they all do the same as @AdamMajer - basically setting `--prefix` to the temporary install dir and then just copy the files to some other prefix (usually `/usr`) at package install time which further confirms our assumption. So, @AdamMajer has a point that this kind of a packaging workflow renders 892221a ineffective again - but not sure what he means by "breaks things".

---

## Comment 9

> Username: dennisklein  
> Created at: 2019-11-19 21:27:27 UTC  
> Updated at: 2019-11-19 21:27:53 UTC  
> Url: https://github.com/boostorg/boost_install/issues/14#issuecomment-555723289  

Instead of using `--stagedir` value, one could always use the `--prefix` value in 892221a (?)  
  
Then, packagers could do  
```  
b2 --prefix=/usr --stagedir=/tmp/boost_stage stage  
b2 --prefix=/tmp/boost_install install  
```  
and then include `/tmp/boost_stage/lib` as `<prefix>/lib` and `/tmp/boost_install/include` as `<prefix>/include` in the package.

---

## Comment 10

> Username: pdimov  
> Created at: 2019-11-19 21:36:20 UTC  
> Url: https://github.com/boostorg/boost_install/issues/14#issuecomment-555726645  

`--prefix` (traditionally) doesn't do anything for `stage`, so I'm not sure.  
  
The fact that Arch (that prompted this issue) packages by installing into a temporary dir basically means that the fix doesn't really fix anything though.  
  
For a proper fix we may need to introduce `--final-prefix` et al, but that's going to be more involved.

---

## Comment 11

> Username: AdamMajer  
> Created at: 2019-11-20 09:39:58 UTC  
> Url: https://github.com/boostorg/boost_install/issues/14#issuecomment-555922900  

Instead of adding additional things, like --final-prefix, add DESTDIR environmental support during `install` phase. We can change the install scripts to just have --prefix=/usr (along with other prefixes) and pass the temporary destination in DESTDIR for package creation.  
  
By "break things", I just meant that the temporary install directory during built is not suppose to be embedded in the produced binary. So it's only broken by policy. It's ineffective by function.

---

## Comment 12

> Username: AdamMajer  
> Created at: 2019-11-21 16:32:10 UTC  
> Url: https://github.com/boostorg/boost_install/issues/14#issuecomment-557165436  

@dennisklein  I've added DESTDIR support to Boost.Build install, but unfortunately, it's magic (like most of Boost.Build) how to actually find the *install* directory, not the staging. But once we have the real install directory in the generated cmake files, it should not be difficult to get DESTDIR prefix removed from there.  
  
https://gist.github.com/AdamMajer/277de44eeec11e5c1ecc0011b2c67cc5  
  
In this gist, the first patched file is the DESTDIR in the main Boost install. The other patched files I'm just experimenting. Maybe someone has a better idea how to fix this? Currently, there is an error because during stage, cmakedir is actually staging, not install directory...  
  
The alternative here is to use something like `sed` to fix the cmake files, but hopefully there is a better way.

---

## Comment 13

> Username: pdimov  
> Created at: 2019-11-21 17:00:36 UTC  
> Url: https://github.com/boostorg/boost_install/issues/14#issuecomment-557178201  

It's not good b2 practice to change behavior based on environment variables. Better to use a `--DESTDIR` option.  
  
Also, I don't think there's any need to touch Boost.Build proper (the parts in `package.jam`) if we're only concerned with installing Boost.  
  
As I said, staging as in `b2 stage` does not correspond to a staged install in the GNU sense. A staged install in this scenario would use `b2 install --prefix=/usr --DESTDIR=/home/build`, not `b2 stage`. `b2 stage` is for a different use case.

---

## Comment 14

> Username: pdimov  
> Created at: 2019-11-21 17:02:14 UTC  
> Url: https://github.com/boostorg/boost_install/issues/14#issuecomment-557178937  

`b2 stage` is probably misnamed, but we're stuck with it now.

---

## Comment 15

> Username: pdimov  
> Created at: 2019-11-21 17:04:25 UTC  
> Url: https://github.com/boostorg/boost_install/issues/14#issuecomment-557179847  

@swatanabe do you have any comments on the suggested `DESTDIR` addition? Should it, if adopted, only affect `b2 install`, or should it automatically modify the directories in `package.jam` too?

---

## Comment 16

> Username: pdimov  
> Created at: 2020-03-22 16:01:08 UTC  
> Url: https://github.com/boostorg/boost_install/issues/14#issuecomment-602231282  

I think that the Arch issue is fixed by #36.

---

## Comment 17

> Username: codyps  
> Created at: 2020-09-02 16:17:19 UTC  
> Updated at: 2020-09-06 20:22:43 UTC  
> Url: https://github.com/boostorg/boost_install/issues/14#issuecomment-685843356  

Yes, I've tried out a local modification to my cmake files that mimics #36 on my arch system and can confirm it fixes builds (I had trouble with pulseview in the past)  
  
Edit: to clarify, I modified _all_ `_BOOST_CMAKEDIR` values to use `REALPATH`. So I changed https://github.com/AlD/boost_install/blob/243ff761e867b1c020f1948ee466378b352516c2/boost-install.jam#L739 to use `REALPATH` too.

---

## Comment 18

> Username: codyps  
> Created at: 2020-11-11 17:17:18 UTC  
> Url: https://github.com/boostorg/boost_install/issues/14#issuecomment-725549651  

A mention of this issue: https://lists.boost.org/boost-users/2020/04/90372.php
