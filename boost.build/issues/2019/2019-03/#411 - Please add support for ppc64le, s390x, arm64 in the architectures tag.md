# #411 - Please add support for ppc64le, s390x, arm64 in the architectures tag [Open]

> Username: xnox  
> Created at: 2019-03-15 14:18:27 UTC  
> Updated at: 2021-06-11 01:55:40 UTC  
> Url: https://github.com/boostorg/build/issues/411  

As mentioned in https://github.com/boostorg/context/issues/102 boost.build doesn't support many modern architectures in the architectures tag, for which in practice, all major linux distributions build boost for.  
  
Please add ppc64le, s390x, arm64 to the architectures tag.  
  
I am happy to assist with providing any data related to these architectures. And one can use launchpad.net ppas, to self-service enable compiling code for all of these arches.  
  
I'll try to write a patch for this too, time permitting.

---

## Comment 1

> Username: grafikrobot  
> Created at: 2019-03-15 16:01:31 UTC  
> Url: https://github.com/boostorg/build/issues/411#issuecomment-473343610  

A patch would be fantastic :-)

---

## Comment 2

> Username: nealef  
> Created at: 2019-04-23 21:00:00 UTC  
> Updated at: 2019-04-23 23:12:07 UTC  
> Url: https://github.com/boostorg/build/issues/411#issuecomment-485972381  

Where should the changes be made? I see:  
  
1. doc/src/reference.adoc  
2. src/tools/builtin.py  
3. src/tools/features/architecture-feature.jam  
4. src/tools/features/instruction-set-feature.jam  
5. src/tools/gcc.jam  
6. src/tools/gcc.py  
  
I was envisaging something like this for s390, but I don't think I have captured everything:  
```  
diff --git a/doc/src/reference.adoc b/doc/src/reference.adoc  
index e9c6016..9f28ad8 100644  
--- a/doc/src/reference.adoc  
+++ b/doc/src/reference.adoc  
@@ -425,7 +425,7 @@ for details of cross-compilation.  
 [[bbv2.builtin.features.architecture]]`architecture`::  
 *Allowed values:* `x86`, `ia64`, `sparc`, `power`, `mips1`, `mips2`,  
 `mips3`, `mips4`, `mips32`, `mips32r2`, `mips64`, `parisc`, `arm`,  
-`combined`, `combined-x86-power`.  
+`s390`, `combined`, `combined-x86-power`.  
 +  
 The `architecture` features specifies the general processor family to  
 generate code for.  
diff --git a/src/tools/builtin.py b/src/tools/builtin.py  
index 1a80c3f..66b4e0e 100644  
--- a/src/tools/builtin.py  
+++ b/src/tools/builtin.py  
@@ -265,6 +265,9 @@ def register_globals ():  
         # Advanced RISC Machines  
         'arm',  
   
+	# z Systems (aka s390)  
+	's390',  
+  
         # Combined architectures for platforms/toolsets that support building for  
         # multiple architectures at once. "combined" would be the default multi-arch  
         # for the toolset.  
@@ -310,7 +313,10 @@ def register_globals ():  
   
         # Advanced RISC Machines  
         'armv2', 'armv2a', 'armv3', 'armv3m', 'armv4', 'armv4t', 'armv5',  
-        'armv5t', 'armv5te', 'armv6', 'armv6j', 'iwmmxt', 'ep9312'],  
+        'armv5t', 'armv5te', 'armv6', 'armv6j', 'iwmmxt', 'ep9312',  
+  
+	# z Systems (aka s390)  
+	'z196', 'zEC12', 'z13', 'z13', 'z14'],  
   
         ['propagated', 'optional'])  
   
diff --git a/src/tools/features/architecture-feature.jam b/src/tools/features/architecture-feature.jam  
index 54f9a8f..807da8c 100644  
--- a/src/tools/features/architecture-feature.jam  
+++ b/src/tools/features/architecture-feature.jam  
@@ -33,6 +33,9 @@ feature.feature architecture  
         # RISC-V  
         riscv  
   
+        # z Systems (aka s390)  
+        s390  
+  
         # Combined architectures for platforms/toolsets that support building for  
         # multiple architectures at once. "combined" would be the default multi-arch  
         # for the toolset.  
diff --git a/src/tools/features/instruction-set-feature.jam b/src/tools/features/instruction-set-feature.jam  
index 080b01d..adfcf72 100644  
--- a/src/tools/features/instruction-set-feature.jam  
+++ b/src/tools/features/instruction-set-feature.jam  
@@ -42,6 +42,9 @@ feature.feature instruction-set  
         # Advanced RISC Machines  
         armv2 armv2a armv3 armv3m armv4 armv4t armv5 armv5t armv5te armv6 armv6j iwmmxt ep9312  
         armv7 armv7s  
+	  
+	# z Systems (aka s390)  
+	z196 zEC12 z13 z14  
   
     :  
         propagated optional  
diff --git a/src/tools/gcc.jam b/src/tools/gcc.jam  
index 11227cb..27eda49 100644  
--- a/src/tools/gcc.jam  
+++ b/src/tools/gcc.jam  
@@ -1294,5 +1294,9 @@ cpu-flags gcc OPTIONS : power : rios1 : -mcpu=rios1 ;  
 cpu-flags gcc OPTIONS : power : rios2 : -mcpu=rios2 ;  
 cpu-flags gcc OPTIONS : power : rsc : -mcpu=rsc ;  
 cpu-flags gcc OPTIONS : power : rs64a : -mcpu=rs64 ;  
+cpu-flags gcc OPTIONS : s390 : z196 : -march=z196 ;  
+cpu-flags gcc OPTIONS : s390 : zEC12 : -march=zEC12 ;  
+cpu-flags gcc OPTIONS : s390 : z13 : -march=z13 ;  
+cpu-flags gcc OPTIONS : s390 : z14 : -march=z14 ;  
 # AIX variant of RS/6000 & PowerPC  
 toolset.flags gcc AROPTIONS <address-model>64/<target-os>aix : "-X64" ;  
diff --git a/src/tools/gcc.py b/src/tools/gcc.py  
index 2fcf185..60c3313 100644  
--- a/src/tools/gcc.py  
+++ b/src/tools/gcc.py  
@@ -860,6 +860,10 @@ cpu_flags('gcc', 'OPTIONS', 'power', 'rios1', ['-mcpu=rios1'])  
 cpu_flags('gcc', 'OPTIONS', 'power', 'rios2', ['-mcpu=rios2'])  
 cpu_flags('gcc', 'OPTIONS', 'power', 'rsc', ['-mcpu=rsc'])  
 cpu_flags('gcc', 'OPTIONS', 'power', 'rs64a', ['-mcpu=rs64'])  
+cpu_flags('gcc', 'OPTIONS', 's390',  'z196', ['-march=z196'])  
+cpu_flags('gcc', 'OPTIONS', 's390',  'zEC12', ['-march=zEC12'])  
+cpu_flags('gcc', 'OPTIONS', 's390',  'z13', ['-march=z13'])  
+cpu_flags('gcc', 'OPTIONS', 's390',  'z14', ['-march=z14'])  
 # AIX variant of RS/6000 & PowerPC  
 flags('gcc', 'OPTIONS', ['<architecture>power/<address-model>32/<target-os>aix'], ['-maix32'])  
 flags('gcc', 'OPTIONS', ['<architecture>power/<address-model>64/<target-os>aix'], ['-maix64'])  
```

---

## Comment 3

> Username: nealef  
> Created at: 2019-04-25 19:08:50 UTC  
> Url: https://github.com/boostorg/build/issues/411#issuecomment-486800841  

Found what the missing bits were. I should have PRs for boost/build/config/context in the near future.

---

## Comment 4

> Username: xnox  
> Created at: 2019-04-25 23:20:33 UTC  
> Url: https://github.com/boostorg/build/issues/411#issuecomment-486871660  

@nealef I dislike 's390' usage above, as that stands for the 31-bit platform nobody uses anymore. And the cpu flags mentioned are all 64-bit, meaning it should be 's390x' throughout. Otherwise it looks good!

---

## Comment 5

> Username: nealef  
> Created at: 2019-04-26 02:45:53 UTC  
> Url: https://github.com/boostorg/build/issues/411#issuecomment-486905986  

True, but the linux kernel arch directory is s390 and s390x is just a superset. Gcc defines both __s390__ and __s390x__ when building with -m64 so I thought I better be consistent. Frankly IBM’s had so many name changes in the last 20 years that I’m glad I’m not buying stationery for them! (S/390, ESA/390, zSeries, System z, z Systems…)  
  
  
@nealef<https://github.com/nealef> I dislike 's390' usage above, as that stands for the 31-bit platform nobody uses anymore. And the cpu flags mentioned are all 64-bit, meaning it should be 's390x' throughout. Otherwise it looks good!

---

## Comment 6

> Username: xnox  
> Created at: 2019-04-26 12:37:52 UTC  
> Url: https://github.com/boostorg/build/issues/411#issuecomment-487041789  

sure, but GNU architecture type s390 is for 31bit only, and s390x is for 64bit only. And what matters here is the toolchain architecture types, not the kernel arches. And you are specifying zarches that are only valid for s390x.  
  
This is like saying that aarch64 armhf armel are all "just arm", when it is different instruction sets and different minimal ABI.  
  
And boost should not claim or pretend to support the 31bit s390 port. Only the s390x port.

---

## Comment 7

> Username: xnox  
> Created at: 2019-04-26 12:39:56 UTC  
> Url: https://github.com/boostorg/build/issues/411#issuecomment-487042352  

I'll use your patch as a start, and will start working on a version that would match current conventions for the s390x ports in all the major linux distributions.

---

## Comment 8

> Username: nealef  
> Created at: 2019-04-26 12:46:00 UTC  
> Url: https://github.com/boostorg/build/issues/411#issuecomment-487044001  

Yep, I’ll make the change.  
  
.

---

## Comment 9

> Username: xnox  
> Created at: 2019-04-26 12:51:07 UTC  
> Url: https://github.com/boostorg/build/issues/411#issuecomment-487045429  

Also, i think you have a duplicated value in: `'z196', 'zEC12', 'z13', 'z13', 'z14'],` but otherwise it looks great and covers all of currently in use mainframe generations.

---

## Comment 10

> Username: nealef  
> Created at: 2019-04-26 12:59:33 UTC  
> Url: https://github.com/boostorg/build/issues/411#issuecomment-487047831  

Correct, I thought there was a z13s in gcc but it was just the model number of the baby z13. I have a patchset that covers boost/build/config/context that I’ve updated with the s390->s390x changes.

---

## Comment 11

> Username: nealef  
> Created at: 2019-04-26 13:02:58 UTC  
> Url: https://github.com/boostorg/build/issues/411#issuecomment-487048791  

Is there a testsuite? The context changes build but I’ve not been able to exercise them. I used them to build the latest ceph but it’s a PITA to set up a ceph cluster just to test one component.

---

## Comment 12

> Username: Naveenaidu  
> Created at: 2019-06-18 09:34:36 UTC  
> Updated at: 2019-06-18 09:40:36 UTC  
> Url: https://github.com/boostorg/build/issues/411#issuecomment-503027451  

~~@nealef What is the status of the patch. I would be glad to help, if you need any :)~~  
  
~~Also, if the patch is ready, could you please send a PR. I am currently working on [s390x support for boost.context](https://github.com/boostorg/context/issues/105) and this patch is really important for it to work~~  
  
Ah! Please don't mind. I just found that the PR has already been merged for this. Thank you. Sorry for the noise.  
  
Also @grafikrobot Could you please close this issue. Since a [patch](https://github.com/boostorg/build/pull/432) has already been sent and merged.

---

## Comment 13

> Username: xnox  
> Created at: 2019-06-18 20:27:18 UTC  
> Url: https://github.com/boostorg/build/issues/411#issuecomment-503298461  

well... @Naveenaidu we still need ppc64le and arm64, no?

---

## Comment 14

> Username: Naveenaidu  
> Created at: 2019-06-19 04:12:28 UTC  
> Updated at: 2019-06-19 04:15:36 UTC  
> Url: https://github.com/boostorg/build/issues/411#issuecomment-503398630  

> well... @Naveenaidu we still need ppc64le and arm64, no?  
  
My bad! I was only focusing on `s390x` and forgot about the title about the issue. It would be great if any reference to the patch can be added here, that specifically says that the issue has been solved for `s390x` arch. I mean, when I initially looked upon the issue, I still thought that the implementation for any of mentioned arch has not been done.  
  
It was only after the inspection of the closed PR's that I found that the support for `s390x` was implemented.

---

## Comment 15

> Username: stale[bot]  
> Created at: 2021-06-11 01:55:02 UTC  
> Url: https://github.com/boostorg/build/issues/411#issuecomment-859203329  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
