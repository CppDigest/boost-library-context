# #229 Feature/cmake config [Merged]

> Username: pdimov  
> Created at: 2018-12-13 16:44:46 UTC  
> Updated at: 2025-10-30 12:13:44 UTC  
> Merged at: 2018-12-18 17:06:52 UTC  
> Closed at: 2018-12-18 17:06:52 UTC  
> Url: https://github.com/boostorg/boost/pull/229  

Now that 1.69 is out, I think it's time to (squash-)merge this. I'll do it myself in a few days if there are no objections.

---

## Comment 1

> Username: swatanabe  
> Created_at: 2018-12-13 17:08:47 UTC  
> Url: https://github.com/boostorg/boost/pull/229#issuecomment-447046504  

AMDG  
  
On 12/13/2018 09:44 AM, Peter Dimov wrote:  
> Now that 1.69 is out, I think it's time to (squash-)merge this. I'll do it myself in a few days if there are no objections.  
>   
  
I do have one request: Can we add some options to control  
this like --[no-]export-cmake and --cmakedir=...?  Also,  
it looks like the include/ and lib/ subdirectories for  
the generated cmake are hard-coded and do not account  
for --libdir/--includedir/--exec-prefix.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: pdimov  
> Created_at: 2018-12-13 17:39:53 UTC  
> Url: https://github.com/boostorg/boost/pull/229#issuecomment-447056422  

A nontrivial request. On one hand the current arrangement doesn't work correctly with --includedir/--libdir/--exec-prefix, on the other it allows you to move the --prefix directory after installation, with the configs still working. I should probably encode the relative paths to --includedir/--libdir starting from --cmakedir, even though this isn't correct either in the case where you move just the --exec-prefix directory somewhere else, with --includedir retaining its location.

---

## Comment 3

> Username: swatanabe  
> Created_at: 2018-12-13 17:57:12 UTC  
> Url: https://github.com/boostorg/boost/pull/229#issuecomment-447061872  

AMDG  
  
On 12/13/2018 10:39 AM, Peter Dimov wrote:  
> A nontrivial request.  
  
I started out with just enable/disable, but it  
kind of snowballed, after I reviewed the existing  
installation options...  
  
> On one hand the current arrangement doesn't work correctly with --includedir/--libdir/--exec-prefix, on the other it allows you to move the --prefix directory after installation, with the configs still working. I should probably encode the relative paths to --includedir/--libdir starting from --cmakedir, even though this isn't correct either in the case where you move just the --exec-prefix directory somewhere else, with --includedir retaining its location.  
>   
  
I think it's probably fine to assume that  
individual subdirectories won't be  
relocated independently.  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: pdimov  
> Created_at: 2018-12-13 18:29:36 UTC  
> Updated_at: 2018-12-13 18:32:29 UTC  
> Url: https://github.com/boostorg/boost/pull/229#issuecomment-447071695  

I was pretty sure that `path.jam` had problems with Windows paths, and it didn't disappoint:  
  
```  
prefix: C:\boost  
exec-prefix: D:\boost  
includedir: C:\boost/include  
libdir: D:\boost/lib  
cmakedir: D:\cmakedir  
--  
includedir relative-to cmakedir: ../C:/boost/include  
libdir relative-to cmakedir: ../D:/boost/lib  
--  
path.make prefix: /C:/boost  
path.make exec-prefix: /D:/boost  
path.make includedir: /C:/boost/include  
path.make libdir: /D:/boost/lib  
path.make cmakedir: /D:/cmakedir  
--  
includedir relative-to cmakedir: ../../C:/boost/include  
libdir relative-to cmakedir: ../boost/lib  
```

---

## Comment 5

> Username: swatanabe  
> Created_at: 2018-12-13 18:49:04 UTC  
> Url: https://github.com/boostorg/boost/pull/229#issuecomment-447077835  

AMDG  
  
On 12/13/2018 11:29 AM, Peter Dimov wrote:  
> I was pretty sure that `path.jam` had problems with Windows paths, and it didn't disappoint:  
>   
> <snip>  
> --  
> includedir relative-to cmakedir: ../../C:/boost/include  
  
This is nonsense, of course, but I have no  
idea what relative-to should do for this.  
  
> libdir relative-to cmakedir: ../boost/lib  
> ```  
>   
  
In Christ,  
Steven Watanabe

---

## Comment 6

> Username: swatanabe  
> Created_at: 2018-12-13 19:14:47 UTC  
> Url: https://github.com/boostorg/boost/pull/229#issuecomment-447086132  

AMDG  
  
If I change path.relative-to to return the second path  
if no relative path exists, that will fix this specific  
problem.  It might do the wrong thing in other places,  
but anything that it breaks is already broken anyhow.  
Another possibility is to pretend that this is a valid  
path and fix it up in path.native.  Thoughts?  
  
You could also get around the problem by using absolute  
paths if prefix is not a common parent of cmakedir and  
libdir/includedir.  
  
I'm actually okay with leaving this alone and just  
saying that it might not work correctly if you try  
to split the install across multiple drives.  
  
In Christ,  
Steven Watanabe

---

## Comment 7

> Username: pdimov  
> Created_at: 2018-12-13 19:16:05 UTC  
> Url: https://github.com/boostorg/boost/pull/229#issuecomment-447086547  

Leave the path absolute is my guess. It still won't quite work as-is in the .cmake file, of course, will need a check.

---

## Comment 8

> Username: swatanabe  
> Created_at: 2018-12-13 23:20:31 UTC  
> Url: https://github.com/boostorg/boost/pull/229#issuecomment-447156987  

AMDG  
  
On 12/13/2018 12:16 PM, Peter Dimov wrote:  
> Leave the path absolute is my guess.  
  
Done.  
  
In Christ,  
Steven Watanabe

---

## Comment 9

> Username: pdimov  
> Created_at: 2018-12-14 15:33:01 UTC  
> Url: https://github.com/boostorg/boost/pull/229#issuecomment-447360135  

I can fairly confidently predict that nobody has ever tried to use `--exec-prefix`, because it doesn't work.  
  
https://github.com/boostorg/build/blob/518bc4a1a1a1980134121070593fabea026dce16/src/tools/package.jam#L83

---

## Comment 10

> Username: swatanabe  
> Created_at: 2018-12-14 15:57:28 UTC  
> Url: https://github.com/boostorg/boost/pull/229#issuecomment-447367489  

AMDG  
  
On 12/14/2018 08:33 AM, Peter Dimov wrote:  
> I can fairly confidently predict that nobody has ever tried to use `--exec-prefix`, because it doesn't work.  
>   
  
That's easy to fix, but if no one is using it, and  
no one has complained about it being broken, we might  
as well undocument and remove it.  
  
> https://github.com/boostorg/build/blob/518bc4a1a1a1980134121070593fabea026dce16/src/tools/package.jam#L83  
>   
  
In Christ,  
Steven Watanabe

---

## Comment 11

> Username: pdimov  
> Created_at: 2018-12-14 17:05:41 UTC  
> Url: https://github.com/boostorg/boost/pull/229#issuecomment-447388295  

It comes from https://www.gnu.org/prep/standards/html_node/Directory-Variables.html, but I don't know whether we should bother.

---

## Comment 12

> Username: pdimov  
> Created_at: 2018-12-14 17:29:07 UTC  
> Url: https://github.com/boostorg/boost/pull/229#issuecomment-447394848  

I'm recomputing the same directories `package.jam` computes two times now, in `tools/boost_install` and `libs/headers`, and this isn't Best Practices. Perhaps we need to think about exporting these from `package.jam` in some manner.

---

## Comment 13

> Username: swatanabe  
> Created_at: 2018-12-14 18:51:57 UTC  
> Url: https://github.com/boostorg/boost/pull/229#issuecomment-447419258  

AMDG  
  
On 12/14/2018 10:29 AM, Peter Dimov wrote:  
> I'm recomputing the same directories `package.jam` computes two times now, in `tools/boost_install` and `libs/headers`, and this isn't Best Practices. Perhaps we need to think about exporting these from `package.jam` in some manner.  
>   
  
It should definitely be centralized, as there are  
quite a few pitfalls.  (For example, if you were to  
move the call to install-cmake-config before package.install  
in boost-install, then the cmake files might be installed  
in the wrong location in some cases.)  
  
I'm working on something like this:  
  
paths = [ new package-paths Boost ] ;  
libdir = [ $(paths).libdir ] ;  
cmakedir = [ $(paths).get cmakedir : cmake : libdir ] ;  
  
In Christ,  
Steven Watanabe

---

## Comment 14

> Username: pdimov  
> Created_at: 2018-12-15 02:23:28 UTC  
> Url: https://github.com/boostorg/boost/pull/229#issuecomment-447529856  

> For example, if you were to move the call to install-cmake-config before package.install in boost-install, then the cmake files might be installed in the wrong location in some cases.  
  
I had noticed that. What I hadn't noticed was that this, too, doesn't work properly once `exec-prefix` is taken into account, because `booststrap.sh` sets `exec-prefix` in `project-config.jam`, but the special handling of `--prefix` doesn't clear `exec-prefix`. Fun.

---

## Comment 15

> Username: swatanabe  
> Created_at: 2018-12-15 04:04:41 UTC  
> Url: https://github.com/boostorg/boost/pull/229#issuecomment-447535295  

AMDG  
  
On 12/14/2018 07:23 PM, Peter Dimov wrote:  
>> For example, if you were to move the call to install-cmake-config before package.install in boost-install, then the cmake files might be installed in the wrong location in some cases.  
>   
> I had noticed that. What I hadn't noticed was that this, too, doesn't work properly once `exec-prefix` is taken into account, because `booststrap.sh` sets `exec-prefix` in `project-config.jam`, but the special handling of `--prefix` doesn't clear `exec-prefix`. Fun.  
>   
  
But that's fine, since exec-prefix is ignored anyway...  
  
I'm in the process of cleaning up package.jam.  
Unfortunately, there are no existing tests for  
it whatsoever, so I'm having to write them as well.  
  
In Christ,  
Steven Watanabe

---

## Comment 16

> Username: pdimov  
> Created_at: 2018-12-15 04:17:57 UTC  
> Url: https://github.com/boostorg/boost/pull/229#issuecomment-447535868  

I wonder what was the point of passing --prefix, --exec-prefix, --includedir, --libdir to bootstrap.sh, instead of to b2 directly. So that you could install more than once without repeating the options each time? Historical reasons?

---
