# #540 Rename mips1 to mips [Merged]

> Username: wzssyqa  
> Created at: 2021-07-22 08:13:55 UTC  
> Updated at: 2022-08-22 01:41:27 UTC  
> Merged at: 2022-05-13 17:17:49 UTC  
> Closed at: 2022-05-13 17:17:49 UTC  
> Url: https://github.com/boostorg/boost/pull/540  

In fact mips1 is now used for all mips revisions.

---

## Comment 1

> Username: mclow  
> Created_at: 2021-07-22 13:02:47 UTC  
> Url: https://github.com/boostorg/boost/pull/540#issuecomment-884894039  

We don't apply pull requests to `master`; we apply them to `develop` and then, after the tests have passed, merge from there to `master`

---

## Comment 2

> Username: wzssyqa  
> Created_at: 2022-05-13 14:36:27 UTC  
> Url: https://github.com/boostorg/boost/pull/540#issuecomment-1126129010  

@mclow you forget this pr before new release, which makes boost1.79 cannot build on mips.

---

## Comment 3

> Username: xen0n  
> Created_at: 2022-05-13 14:48:42 UTC  
> Url: https://github.com/boostorg/boost/pull/540#issuecomment-1126140797  

And ia64, loongarch64 too: https://bugs.gentoo.org/843212  
  
Perhaps cut a patch release for fixing this? I'm not entirely familiar with the "hotfix" procedure of Boost, so just do whatever needed. We'll carry patches downstream in the meantime so don't worry too much...

---

## Comment 4

> Username: pdimov  
> Created_at: 2022-05-13 15:42:00 UTC  
> Url: https://github.com/boostorg/boost/pull/540#issuecomment-1126191552  

I note that the last comment in https://bugs.gentoo.org/843212 says that applying this doesn't fix the issue though.

---

## Comment 5

> Username: ffontaine  
> Created_at: 2022-05-15 20:24:57 UTC  
> Url: https://github.com/boostorg/boost/pull/540#issuecomment-1127020717  

I don't know if this is related but we also have build failures on buildroot on arc and m68k since boost 1.79.0 and https://github.com/boostorg/log/commit/3cbc2585c3b7ff5d884a6c337a99525b57fa184b:  
```  
  
cd /home/buildroot/autobuild/instance-0/output-1/build/boost-1.79.0 && PATH="/home/buildroot/autobuild/instance-0/output-1/host/bin:/home/buildroot/autobuild/instance-0/output-1/host/sbin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin" ./b2 --no-cmake-config toolset=gcc threading=multi abi=sysv variant=debug -j4 -q --ignore-site-config --layout=system --user-config=/home/buildroot/autobuild/instance-0/output-1/build/boost-1.79.0/user-config.jam link=shared runtime-link=shared -d+1  
Performing configuration checks  
  
    - default address-model    : 32-bit [1]  
    - default architecture     : none [1]  
  
Building the Boost C++ Libraries.  
  
  
    - x86                      : no [2]  
    - arm                      : no [2]  
    - mips1                    : no [2]  
    - power                    : no [2]  
    - sparc                    : no [2]  
    - has std::atomic_ref      : no [2]  
    - has statx                : no [2]  
    - has statx syscall        : no [2]  
    - has init_priority attribute : yes [2]  
    - has stat::st_blksize     : yes [2]  
    - has stat::st_mtim        : yes [2]  
    - has stat::st_mtimensec   : no [2]  
    - has stat::st_mtimespec   : no [2]  
    - has stat::st_birthtim    : no [2]  
    - has stat::st_birthtimensec : no [2]  
    - has stat::st_birthtimespec : no [2]  
    - has fdopendir(O_NOFOLLOW) : yes [2]  
    - cxx11_constexpr          : yes [2]  
    - cxx11_decltype           : yes [2]  
    - cxx11_hdr_tuple          : yes [2]  
    - cxx11_template_aliases   : yes [2]  
    - cxx11_variadic_templates : yes [2]  
    - cxx11_basic_alignas      : yes [2]  
    - native atomic int32 supported : yes [2]  
error: at libs/log/build/Jamfile.v2:59  
error: Unable to find file or target named  
error:     '/boost/architecture//mips'  
error: referred to from project at  
error:     'libs/log/build'  
```  
  
Applying this patch doesn't fix the issue. The only way to fix it on my side is to revert https://github.com/boostorg/log/commit/3cbc2585c3b7ff5d884a6c337a99525b57fa184b  
  
Full build log: http://autobuild.buildroot.org/results/edc/edcc7c7f3586993a77b6cc06ed02363a42c09a83/build-end.log

---

## Comment 6

> Username: pdimov  
> Created_at: 2022-05-15 22:17:07 UTC  
> Url: https://github.com/boostorg/boost/pull/540#issuecomment-1127061800  

That's odd; https://github.com/boostorg/config/pull/390 has been applied, and it adds `/boost/architecture//mips`.  
  
@Lastique because Log.

---

## Comment 7

> Username: Lastique  
> Created_at: 2022-05-15 23:24:07 UTC  
> Url: https://github.com/boostorg/boost/pull/540#issuecomment-1127084865  

Boost.Log was changed in https://github.com/boostorg/log/pull/155. After the dependent change in Boost.Build in https://github.com/bfgroup/b2/pull/58 was merged.  
  
Looks like Boost.Config was not merged to master before 1.79 release.

---

## Comment 8

> Username: pdimov  
> Created_at: 2022-05-16 00:31:40 UTC  
> Url: https://github.com/boostorg/boost/pull/540#issuecomment-1127100180  

Yeah, the relevant commit https://github.com/boostorg/config/commit/6393b50d793f2e7d723f593e8cc6f4bff6174b69 is only on develop, still.  
  
@jzmaddock

---

## Comment 9

> Username: Lastique  
> Created_at: 2022-05-16 11:14:53 UTC  
> Updated_at: 2022-05-16 11:15:44 UTC  
> Url: https://github.com/boostorg/boost/pull/540#issuecomment-1127540591  

It is in master. Just not the 1.79 tag.  
  
Perhaps, a patch in the release notes is in order.

---

## Comment 10

> Username: ClaymorePT  
> Created_at: 2022-08-22 01:41:07 UTC  
> Updated_at: 2022-08-22 01:41:27 UTC  
> Url: https://github.com/boostorg/boost/pull/540#issuecomment-1221692964  

For documentation purposes, this was still required -> https://github.com/openwrt/packages/pull/19221 in order to make boost.context visible for openwrt when building for mips64 arch

---
