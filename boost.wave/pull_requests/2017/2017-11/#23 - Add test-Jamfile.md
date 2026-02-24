# #23 Add test/Jamfile [Merged]

> Username: pdimov  
> Created at: 2017-11-28 21:45:05 UTC  
> Updated at: 2017-12-21 07:12:13 UTC  
> Merged at: 2017-11-28 21:50:19 UTC  
> Closed at: 2017-11-28 21:50:19 UTC  
> Url: https://github.com/boostorg/wave/pull/23  

This adds a `Jamfile` in `test` as `wave` is the only library for which `b2 libs/libname/test` doesn't work. This would allow the elimination of the special case at https://github.com/boostorg/boost/blob/master/status/Jamfile.v2#L199.

---

## Review 1 [Approved]

> Username: hkaiser  
> Created_at: 2017-11-28 21:50:15 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/23#pullrequestreview-79668881  

LGTM, thanks!

---

## Comment 2

> Username: pdimov  
> Created_at: 2017-11-29 14:24:07 UTC  
> Url: https://github.com/boostorg/wave/pull/23#issuecomment-347874982  

Thanks. You can see the results of merging this in the Travis run on the superproject:  
  
https://travis-ci.org/boostorg/boost/builds/308673108  
  
Specifically, in the 8418.5 and 8418.6 jobs:  
  
https://travis-ci.org/boostorg/boost/jobs/308673120  
https://travis-ci.org/boostorg/boost/jobs/308673121  
  
The Appveyor run of the same commit is at  
  
https://ci.appveyor.com/project/boostorg/boost/build/1.0.4915-develop  
  
It fails because, I suspect, the same happens there as in my local tests with MSVC, namely, `testwave.exe` throws up an assertion dialog box ("string iterators are incompatible") and the job times out. I haven't had time to investigate this.

---

## Comment 3

> Username: pdimov  
> Created_at: 2017-11-29 15:25:28 UTC  
> Url: https://github.com/boostorg/wave/pull/23#issuecomment-347894353  

The MSVC error occurs here: https://github.com/boostorg/wave/blob/develop/include/boost/wave/cpplexer/re2clex/cpp_re.hpp#L222  
  
The debug iterator check insists that you can't compare a valid iterator to a singular one. I'm not entirely sure it's correct from a standard perspective.

---

## Comment 4

> Username: hkaiser  
> Created_at: 2017-11-29 15:52:42 UTC  
> Url: https://github.com/boostorg/wave/pull/23#issuecomment-347903700  

@jefftrull this looks like to be caused by your recent changes. Would you mind having a look?

---

## Comment 5

> Username: jefftrull  
> Created_at: 2017-11-29 17:16:14 UTC  
> Url: https://github.com/boostorg/wave/pull/23#issuecomment-347930953  

I'll get right on it.

---

## Comment 6

> Username: pdimov  
> Created_at: 2017-11-29 17:40:05 UTC  
> Url: https://github.com/boostorg/wave/pull/23#issuecomment-347938010  

I tried changing `s->act != Iterator()` to `s->act != s->last` and this got rid of the assertions, but tests started failing, so I assumed that it's not the right fix. On second look, however, the tests fail on`master` as well, so perhaps it's the right fix after all. The failures are of the form  
  
```  
C:\Projects\boost-git\boost\libs\wave\test\testwave\testfiles\t_6_029.cpp: failed  
error result:  
t_6_029.hpp(47): warning: unbalanced #if/#endif in include file: C:/Projects\\boost-git\\boost\\libs\\wave\\test\\testwave\\testfiles\\t_6_029.hpp  
  
expected error:  
t_6_029.hpp(47): warning: unbalanced #if/#endif in include file: C:\\Projects\\boost-git\\boost\\libs\\wave\\test\\testwave\\testfiles\\t_6_029.hpp  
  
C:\Projects\boost-git\boost\libs\wave\test\testwave\testfiles\t_6_030.cpp: failed  
error result:  
t_6_030.hpp(49): warning: unbalanced #if/#endif in include file: C:/Projects\\boost-git\\boost\\libs\\wave\\test\\testwave\\testfiles\\t_6_030.hpp  
  
expected error:  
t_6_030.hpp(49): warning: unbalanced #if/#endif in include file: C:\\Projects\\boost-git\\boost\\libs\\wave\\test\\testwave\\testfiles\\t_6_030.hpp  
```  
  
That is, there's a mismatch between `C:/Projects` and `C:\\Projects` for some reason.

---

## Comment 7

> Username: pdimov  
> Created_at: 2017-11-29 17:47:45 UTC  
> Url: https://github.com/boostorg/wave/pull/23#issuecomment-347940256  

On msvc-8.0 - don't know if it's still supported - there are many more errors, of the form  
  
```  
C:\Projects\boost-git\boost\libs\wave\test\testwave\testfiles\t_9_023.cpp: failed  
result:  
#line zd "t_9_023.cpp"  
_C_LIB_DECL  
  
expected:  
#line 18 "t_9_023.cpp"  
_C_LIB_DECL  
  
C:\Projects\boost-git\boost\libs\wave\test\testwave\testfiles\t_9_024.cpp: succeeded  
testwave: 90 of 200 test(s) succeeded (110 test(s) failed).  
```  
  
That is, the `#line` directive shows `zd` instead of a line number.

---

## Comment 8

> Username: pdimov  
> Created_at: 2017-11-29 17:53:03 UTC  
> Url: https://github.com/boostorg/wave/pull/23#issuecomment-347941767  

Same `zd` errors on msvc-10.0, 11.0, 12.0. 14.0 and 14.1 no longer have them.

---

## Comment 9

> Username: jefftrull  
> Created_at: 2017-11-29 18:21:54 UTC  
> Url: https://github.com/boostorg/wave/pull/23#issuecomment-347950024  

Hi Peter, can you clarify whether the "zd" errors are observed when you apply the (s->act != s->last) hack, or without it (i.e., as the code currently stands)?  Thank you.

---

## Comment 10

> Username: pdimov  
> Created_at: 2017-11-29 18:23:07 UTC  
> Url: https://github.com/boostorg/wave/pull/23#issuecomment-347950373  

The `zd` errors are on the master branch, which doesn't need the change (which is not a hack :-) ).

---

## Comment 11

> Username: jefftrull  
> Created_at: 2017-11-29 22:19:45 UTC  
> Url: https://github.com/boostorg/wave/pull/23#issuecomment-348016002  

I withdraw the characterization :)

---

## Comment 12

> Username: jefftrull  
> Created_at: 2017-11-29 23:30:10 UTC  
> Updated_at: 2017-11-30 16:46:16 UTC  
> Url: https://github.com/boostorg/wave/pull/23#issuecomment-348031964  

I've reproduced the iterator complaints and will start debugging them soon.  The "C:/" vs "C:\\\\" issue appears to have been present as far back as 2012.  Haven't seen the "zd" thing yet but it may be masked by other problems.  Running MSVC 2017 now.

---

## Comment 13

> Username: pdimov  
> Created_at: 2017-11-29 23:37:47 UTC  
> Url: https://github.com/boostorg/wave/pull/23#issuecomment-348033459  

`zd` is only on 2013 and below. 2015/2017 don't have it.

---

## Comment 14

> Username: jefftrull  
> Created_at: 2017-11-30 02:35:58 UTC  
> Url: https://github.com/boostorg/wave/pull/23#issuecomment-348064422  

[This PR](https://github.com/boostorg/wave/pull/25) should resolve the iterator issue.  I am looking into some of the other problems (there seems to be a linker issue, also?)

---

## Comment 15

> Username: jefftrull  
> Created_at: 2017-11-30 03:32:14 UTC  
> Url: https://github.com/boostorg/wave/pull/23#issuecomment-348072546  

And [this](https://github.com/boostorg/wave/pull/26) should do the same for the linker errors

---

## Comment 16

> Username: jefftrull  
> Created_at: 2017-11-30 06:47:48 UTC  
> Url: https://github.com/boostorg/wave/pull/23#issuecomment-348097966  

The `unbalanced #if/#endif` miscompares are happening because Boost.Filesystem's path normalization function changes the root path separator.  See [here](https://stackoverflow.com/questions/27110973/boost-filesystem-canonical-path-is-not-valid-after-conversion-to-const-char) for a detailed discussion. I applied the suggested workaround in [this PR](https://github.com/boostorg/wave/pull/27).

---

## Comment 17

> Username: jefftrull  
> Created_at: 2017-11-30 18:54:01 UTC  
> Url: https://github.com/boostorg/wave/pull/23#issuecomment-348284757  

It looks like the "zd" is coming from [here](https://github.com/boostorg/wave/blob/develop/include/boost/wave/util/cpp_iterator.hpp#L812) - perhaps sprintf is broken in msvc-12.0 for the "%zd" format?

---

## Comment 18

> Username: pdimov  
> Created_at: 2017-11-30 19:01:34 UTC  
> Url: https://github.com/boostorg/wave/pull/23#issuecomment-348287034  

`%zd` is probably not supported on msvc-12.0 or below.

---

## Comment 19

> Username: pdimov  
> Created_at: 2017-11-30 19:03:08 UTC  
> Url: https://github.com/boostorg/wave/pull/23#issuecomment-348287484  

https://msdn.microsoft.com/en-us/library/tcxf1dw6.aspx  
  
"The hh, j, z, and t length prefixes are not supported."

---

## Comment 20

> Username: pdimov  
> Created_at: 2017-11-30 19:03:35 UTC  
> Url: https://github.com/boostorg/wave/pull/23#issuecomment-348287614  

It appears to work on msvc-14.0 though, despite what the doc says.

---

## Comment 21

> Username: pdimov  
> Created_at: 2017-11-30 19:04:40 UTC  
> Url: https://github.com/boostorg/wave/pull/23#issuecomment-348287955  

The 2017 docs list it as supported: https://docs.microsoft.com/en-us/cpp/c-runtime-library/format-specification-syntax-printf-and-wprintf-functions#size

---

## Comment 22

> Username: jefftrull  
> Created_at: 2017-11-30 19:33:11 UTC  
> Url: https://github.com/boostorg/wave/pull/23#issuecomment-348295948  

I replaced it with lexical_cast in [this PR](https://github.com/boostorg/wave/pull/29) if that's a desirable solution.

---

## Comment 23

> Username: pdimov  
> Created_at: 2017-12-01 04:07:30 UTC  
> Url: https://github.com/boostorg/wave/pull/23#issuecomment-348396125  

Appveyor is green now: https://ci.appveyor.com/project/boostorg/boost/build/1.0.4932-develop  
The matrix http://www.boost.org/development/tests/develop/developer/wave.html will also turn green in a few days. Even the `__boost_check_library__` row has been fixed, by this PR in combination with https://github.com/boostorg/boost/commit/3af09d83695bc1dda0567d0cc7570f3639bda59e.  
  
Good work, us. :-)

---

## Comment 24

> Username: pdimov  
> Created_at: 2017-12-21 00:08:29 UTC  
> Url: https://github.com/boostorg/wave/pull/23#issuecomment-353219344  

Would it be possible to have this commit merged to master? I want to merge the superproject change.

---

## Comment 25

> Username: hkaiser  
> Created_at: 2017-12-21 07:08:46 UTC  
> Url: https://github.com/boostorg/wave/pull/23#issuecomment-353275098  

Uhh, I thought I had merged it... Sorry. Will do.

---

## Comment 26

> Username: hkaiser  
> Created_at: 2017-12-21 07:12:13 UTC  
> Url: https://github.com/boostorg/wave/pull/23#issuecomment-353275671  

Done. Thanks!

---
