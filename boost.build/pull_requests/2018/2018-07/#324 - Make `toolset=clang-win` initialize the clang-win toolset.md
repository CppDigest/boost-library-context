# #324 Make `toolset=clang-win` initialize the clang-win toolset [Merged]

> Username: pdimov  
> Created at: 2018-07-09 12:47:37 UTC  
> Updated at: 2021-10-02 21:00:05 UTC  
> Merged at: 2018-10-27 17:17:18 UTC  
> Closed at: 2018-10-27 17:17:18 UTC  
> Url: https://github.com/boostorg/build/pull/324  

This is more an RFC although it can be applied as-is; it makes `toolset=clang-win` initialize the `clang-win` toolset instead of `clang-linux` with a version `win` which is rarely what one wants.  
  
It still doesn't work because then `clang-win` complains of missing `<compatibility>`, and I see no way of supplying that from the command line. Ideally, we should strive for `toolset=clang-win` working out of the box, but I don't know how feasible this is.

---

## Comment 1

> Username: pdimov  
> Created_at: 2018-10-27 01:30:38 UTC  
> Url: https://github.com/boostorg/build/pull/324#issuecomment-433580346  

Any objections to this one?

---

## Comment 2

> Username: pdimov  
> Created_at: 2018-10-27 01:36:53 UTC  
> Url: https://github.com/boostorg/build/pull/324#issuecomment-433580791  

It starts working once you have a working `clang-win.jam` :-)

---

## Comment 3

> Username: eldiener  
> Created_at: 2018-10-27 01:54:11 UTC  
> Url: https://github.com/boostorg/build/pull/324#issuecomment-433581895  

Since Boost Build comes with a clang-win.jam shouldn't toolset=clang-win go directly to the init routine in that file ? Or does b2 only parse this as toolset=clang version=win and therefore use your PR logic above ?

---

## Comment 4

> Username: pdimov  
> Created_at: 2018-10-27 01:57:41 UTC  
> Url: https://github.com/boostorg/build/pull/324#issuecomment-433582121  

That is what happens for me, yes. Without this patch, `toolset=clang-win` initializes `clang-linux` with a version `win`.

---

## Comment 5

> Username: eldiener  
> Created_at: 2018-10-27 02:03:30 UTC  
> Url: https://github.com/boostorg/build/pull/324#issuecomment-433582469  

I am good with this PR.

---

## Review 6 [Changes requested]

> Username: grafikrobot  
> Created_at: 2018-10-27 02:18:48 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/build/pull/324#pullrequestreview-169040179  

Would it not be better to di `if [ os.name ] = NT`? And that way plain old `toolset=clang` or `using clang ;` on Windows would choose it. Making it uniform with the other clang style toolset variants.

---

## Comment 7

> Username: eldiener  
> Created_at: 2018-10-27 02:26:26 UTC  
> Url: https://github.com/boostorg/build/pull/324#issuecomment-433583764  

The problem with` if [ os.name ] = NT` is that it would then also apply to running clang on Windows targeting mingw-64/gcc, and in that particular case we want clang-linux and not clang-win.

---

## Comment 8

> Username: pdimov  
> Created_at: 2018-10-27 02:28:51 UTC  
> Url: https://github.com/boostorg/build/pull/324#issuecomment-433583905  

Status quo is that `clang-linux` works (even when targeting llvm-win) and `clang-win` does not, so it's not yet appropriate to switch the default. -)

---

## Comment 9

> Username: pdimov  
> Created_at: 2018-10-27 16:59:18 UTC  
> Url: https://github.com/boostorg/build/pull/324#issuecomment-433637275  

Well?

---

## Comment 10

> Username: grafikrobot  
> Created_at: 2018-10-27 17:32:01 UTC  
> Url: https://github.com/boostorg/build/pull/324#issuecomment-433639885  

Ah... I was still thinking about this.  
  
PS. The status quo is incorrect. We shouldn't be defaulting like this.

---

## Comment 11

> Username: pdimov  
> Created_at: 2018-10-27 17:50:24 UTC  
> Url: https://github.com/boostorg/build/pull/324#issuecomment-433641382  

I think that dispatching when the version is `win`, `linux` or `darwin` is correct. This is the case when `toolset=clang-win` is explicitly given and misparsed.  
  
If none of those is the case, that is, `toolset=clang` is given, we probably should switch to defaulting to `clang-win` on Windows, in due time.

---

## Comment 12

> Username: grafikrobot  
> Created_at: 2018-10-27 17:55:54 UTC  
> Url: https://github.com/boostorg/build/pull/324#issuecomment-433641838  

Right.. Definitely trying to respond to the version as win, linux, or darwin is the way to go. But we need to test what happens for `clang-win-8` or `clang-win-7`, etc. As those should also correctly pick the platform and version aspects.  
  
As for the default, we need some meaningful default based on the platform. So defaulting to the corresponding platform specific one based on OS seems reasonable. But as before, the problem child is mingw. Which, IMO, in an ideal world `clang-win.jam` would also handle.

---

## Comment 13

> Username: pdimov  
> Created_at: 2018-10-27 18:09:19 UTC  
> Url: https://github.com/boostorg/build/pull/324#issuecomment-433642903  

The mingw clang works when using clang-linux, and there's no real need to force clang-win (which is msvc-derived) to also contain the functionality of clang-linux (which is gcc-derived).  
  
These two toolsets are more properly called clang-msvc and clang-gcc because that's what they are; the platform itself doesn't really matter.

---

## Comment 14

> Username: swatanabe  
> Created_at: 2018-10-27 18:11:25 UTC  
> Url: https://github.com/boostorg/build/pull/324#issuecomment-433643069  

AMDG  
  
On 10/27/2018 11:55 AM, Rene Rivera wrote:  
> Right.. Definitely trying to respond to the version as win, linux, or darwin is the way to go. But we need to test what happens for `clang-win-8` or `clang-win-7`, etc. As those should also correctly pick the platform and version aspects.  
>   
> As for the default, we need some meaningful default based on the platform. So defaulting to the corresponding platform specific one based on OS seems reasonable.  
  
Could we instead check  
a) If a compiler path is passed, identify it as clang or clang-cl  
b) If no compiler path is given, search for clang-cl and clang++  
   in PATH (in that order for os.name = NT) and choose the  
   appropriate toolset.  
  
> But as before, the problem child is mingw. Which, IMO, in an ideal world `clang-win.jam` would also handle.  
>   
  
That doesn't make any sense from an implementation POV.  
If we're going to go down that route, we should just get  
rid of the different clang-*.jam and have a single clang  
toolset.  
  
In Christ,  
Steven Watanabe

---

## Comment 15

> Username: grafikrobot  
> Created_at: 2018-10-27 18:14:17 UTC  
> Url: https://github.com/boostorg/build/pull/324#issuecomment-433643254  

On Sat, Oct 27, 2018 at 1:11 PM swatanabe <notifications@github.com> wrote:  
  
> AMDG  
>  
> On 10/27/2018 11:55 AM, Rene Rivera wrote:  
> > Right.. Definitely trying to respond to the version as win, linux, or  
> darwin is the way to go. But we need to test what happens for `clang-win-8`  
> or `clang-win-7`, etc. As those should also correctly pick the platform and  
> version aspects.  
> >  
> > As for the default, we need some meaningful default based on the  
> platform. So defaulting to the corresponding platform specific one based on  
> OS seems reasonable.  
>  
> Could we instead check  
> a) If a compiler path is passed, identify it as clang or clang-cl  
> b) If no compiler path is given, search for clang-cl and clang++  
> in PATH (in that order for os.name = NT) and choose the  
> appropriate toolset.  
>  
  
Sounds good to me.  
  
> But as before, the problem child is mingw. Which, IMO, in an ideal world  
> `clang-win.jam` would also handle.  
> >  
>  
> That doesn't make any sense from an implementation POV.  
>  
  
Good point. And I like Peter's idea of renaming to better match what they  
actually are (I didn't realize that's what they where until now).

---

## Comment 16

> Username: eldiener  
> Created_at: 2018-10-27 20:15:04 UTC  
> Url: https://github.com/boostorg/build/pull/324#issuecomment-433651864  

On Windows clang targeting mingw-64/gcc and clang targeting vc++ are two entirely different compilers. Insisting that they both should be the same does not make them the same. Trying to corral both into the same .jam file seems wrong to me.

---

## Comment 17

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:00:04 UTC  
> Url: https://github.com/boostorg/build/pull/324#issuecomment-932819738  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
