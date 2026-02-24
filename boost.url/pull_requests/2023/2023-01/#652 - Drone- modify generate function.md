# #652 Drone: modify generate function [Merged]

> Username: sdarwin  
> Created at: 2023-01-09 21:05:32 UTC  
> Updated at: 2023-01-12 22:46:21 UTC  
> Merged at: 2023-01-12 22:11:28 UTC  
> Closed at: 2023-01-12 22:11:28 UTC  
> Url: https://github.com/boostorg/url/pull/652  

- Upgrade s390x version, which is required so that beast will pass the tests.     
  
@alandefreitas questions:   
  
- Could examples be provided for all the sorts of formats parse_semver_range() takes?  This example is clear 'gcc >=4.8', but is there something like '11 >= gcc >= 4.8' which restricts both ends of the range?     
  
- The code for parse_semver_range() mentions "=" and "^".  When I set a range to just "=11" or "^11", that didn't work. Should it?  
  
- The function seems to have "||" as a delimiter.  Does that signify logical "or"?   Should logical and "&&"  be included too?  What is an example of a range using "||" and does it generate the expected jobs?

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-01-09 21:19:30 UTC  
> Url: https://github.com/boostorg/url/pull/652#issuecomment-1376332061  

GCOVR code coverage report [https://652.url.prtest.cppalliance.org/gcovr/index.html](https://652.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://652.url.prtest.cppalliance.org/genhtml/index.html](https://652.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://652.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://652.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 2

> Username: alandefreitas  
> Created_at: 2023-01-09 21:44:43 UTC  
> Updated_at: 2023-01-09 21:45:21 UTC  
> Url: https://github.com/boostorg/url/pull/652#issuecomment-1376369878  

> Upgrade s390x version, which is required so that beast will pass the tests.  
  
Great :)  
  
> Could examples be provided for all the sorts of formats parse_semver_range() takes?   
  
Sure. Where would these examples go?  
  
> This example is clear 'gcc >=4.8', but is there something like '11 >= gcc >= 4.8' which restricts both ends of the range?  
  
That's interesting. I replicated the main operators of the nodejs [semver syntax](https://github.com/npm/node-semver) but I didn't implement conjunctions, in which your example would be `gcc >= 4.8 <11` or `gcc 4.8 - 11`.  
  
This result would be achievable with disjunctions for now (`||`), which is not great. I guess most people don't even know conjunctions exist because we don't usually want to constraint third-party libraries outside the compatible range so `~`/`^` is always enough.  
  
But I can adapt `parse_semver_range` to understand this other operator.  
  
> The code for parse_semver_range() mentions "=" and "^". When I set a range to just "=11" or "^11", that didn't work. Should it?  
  
`=` should work only if the version is declared as something equivalent to `11.0.0` (`11`, `11.0`, or `11.0.0`). `^11` should match `>=11.0.0 <12.0.0`.   
  
There might be bugs because `=` and `^` ended up not being as useful in the generator as in dependency lists because `^` would get all valid versions in the generator but `^` would usually get the most recent version in the range.  
  
I'll experiment with them and make sure they are working.  
  
> The function seems to have "||" as a delimiter. Does that signify logical "or"?   
  
Exactly.  
  
> Should logical and "&&" be included too?   
  
The convention for conjunctions is simply nothing. `>9 <11` rather than `>9 && <11`. I didn't have a use case for that but it's definitely doable.  
  
> What is an example of a range using "||" and does it generate the expected jobs?  
  
Yes... Bugs aside, something like `gcc ^5 || ^6` is supposed to work.

---

## Comment 3

> Username: sdarwin  
> Created_at: 2023-01-09 22:20:40 UTC  
> Url: https://github.com/boostorg/url/pull/652#issuecomment-1376410708  

> Where would these examples go?  
  
How about here? https://github.com/CPPAlliance/drone-ci/blob/master/docs/generate.md  
  
Adding a comment at the top of the generate() function pointing to that readme file. However if it's currently under active development you might temporarily place it here instead:  
https://github.com/boostorg/url/blob/develop/.drone/generate.md  
  
>  I didn't implement conjunctions  
  
so it might not be functional yet...     
  
A use case is that boostorg/beast drone file did not yet add clang 15 because that was failing, so the compiler range should be a bounded range which removes clang 15 from the top of the range. Such as '14 >= clang >=3.8'  or similar.  
  
> This result would be achievable with disjunctions  
  
if that would work? include in the docs  
  
> = should work only if the version is declared as something equivalent to 11.0.0 (11, 11.0, or 11.0.0). ^11 should match >=11.0.0 <12.0.0  
  
> something like gcc ^5 || ^6 is supposed to work.  
  
- `gcc ^5 || ^6` seems to only generate gcc 5, not 6.  
  
- `gcc =5`  didn't generate anything.

---

## Comment 4

> Username: sdarwin  
> Created_at: 2023-01-09 22:31:56 UTC  
> Url: https://github.com/boostorg/url/pull/652#issuecomment-1376424570  

by the way, maybe syntax such as specifically `gcc ^5` or `=5` doesn't even need to work in drone.  just enough to be functional.   so the examples can say "here are the options you have,  but these other formats haven't been implement."

---

## Comment 5

> Username: alandefreitas  
> Created_at: 2023-01-09 23:31:49 UTC  
> Url: https://github.com/boostorg/url/pull/652#issuecomment-1376490592  

> A use case is that boostorg/beast drone file did not yet add clang 15 because that was failing, so the compiler range should be a bounded range which removes clang 15 from the top of the range. Such as '14 >= clang >=3.8' or similar.  
  
Yes. Adding conjunctions is not a big problem. I didn't really need this feature in boost.url. My first intuition was that we do want to find out about new compilers that fail and keep that bothering us until we fix it.   
  
Maybe limiting the highest version could be used temporarily to fix another bug, but I thought consumers would ideally keep the highest version available so they find out about new bugs in new compilers as soon as they are available.  
  
In a way, implementing conjunctions would make things simpler because things like `^`, `~`, `*`, etc could all be converted to conjunctions.  
  
> gcc ^5 || ^6 seems to only generate gcc 5, not 6.  
  
I'll have a look. It's kind of hard to debug starlark. The python code always needs to be adapted and there's no `print` or anything.

---

## Comment 6

> Username: sdarwin  
> Created_at: 2023-01-09 23:57:48 UTC  
> Url: https://github.com/boostorg/url/pull/652#issuecomment-1376510872  

Wait.  
There had been an issue with beast and clang-15. But now that might only be ubsan, and the regular build is ok.

---

## Comment 7

> Username: sdarwin  
> Created_at: 2023-01-10 01:51:31 UTC  
> Url: https://github.com/boostorg/url/pull/652#issuecomment-1376612340  

clang-15 is succeeding today. So then most of this discussion about semver becomes less critical..    
What could be interesting is to add FreeBSD to `generate`.   Beast has drone jobs building on that operating system.

---

## Comment 8

> Username: sdarwin  
> Created_at: 2023-01-11 17:19:18 UTC  
> Url: https://github.com/boostorg/url/pull/652#issuecomment-1379217373  

the latest commit  
- adds FreeBSD  
- rearranges msvc names  
  
"x64" means 64-bit. In that context, "x86" would be 32-bit. So, change the 32-bit job to "x86".

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2023-01-11 17:30:29 UTC  
> Url: https://github.com/boostorg/url/pull/652#issuecomment-1379239539  

GCOVR code coverage report [https://652.url.prtest.cppalliance.org/gcovr/index.html](https://652.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://652.url.prtest.cppalliance.org/genhtml/index.html](https://652.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://652.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://652.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Review 10 [Commented]

> Username: alandefreitas  
> Created_at: 2023-01-11 20:14:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/652#pullrequestreview-1244539559  

📁 .drone.star

```diff
 177 |-             'x64-msvc': 'MSVC (x64)'
 179 |+             'freebsd-clang': 'FreeBSD-Clang',
 180 |+             'msvc': 'MSVC',
```

> Username: alandefreitas  
> Created_at: 2023-01-11 20:06:43 UTC  
> Updated_at: 2023-01-11 20:14:09 UTC  
> Url: https://github.com/boostorg/url/pull/652#discussion_r1067410632  

I think vinnie wanted `MSVC (x64)` for `msvc`.

> Username: sdarwin  
> Created_at: 2023-01-11 20:26:56 UTC  
> Updated_at: 2023-01-11 20:26:57 UTC  
> Url: https://github.com/boostorg/url/pull/652#discussion_r1067426488  

just added (x64)

---

📁 .drone.star

```diff
 206 |         elif type == 'ubsan':
 202 |-             name = 'UBSsan (' + name + ')'
 207 |+             name = 'UBSan (' + name + ')'
```

> Username: alandefreitas  
> Created_at: 2023-01-11 20:07:01 UTC  
> Updated_at: 2023-01-11 20:14:09 UTC  
> Url: https://github.com/boostorg/url/pull/652#discussion_r1067410832  

:)

---

📁 .drone.star

```diff
 448 |         'apple-clang': ['13.4.1'],
 431 |-         's390x-clang': ['12'],
 449 |+         's390x-clang': ['15'],
```

> Username: alandefreitas  
> Created_at: 2023-01-11 20:14:04 UTC  
> Updated_at: 2023-01-11 20:14:09 UTC  
> Url: https://github.com/boostorg/url/pull/652#discussion_r1067416407  

We now have  
  
```console  
>>>>> APT: INSTALL clang-15 libstdc++-10-dev...  
...  
E: Unable to locate package clang-15  
```  
  
Unfortunately, I see it already uses ubuntu 22.04 `droneubuntu2204:multiarch` so it's the same version as the "Linux Clang 15 (Latest)" job. That's the only idea I would have to fix this.   
  
But maybe there's another library already using this VM and my comments are worthless.

> Username: sdarwin  
> Created_at: 2023-01-11 20:27:17 UTC  
> Url: https://github.com/boostorg/url/pull/652#discussion_r1067426874  

switched s390x back to clang 14.


---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2023-01-11 20:40:00 UTC  
> Url: https://github.com/boostorg/url/pull/652#issuecomment-1379458483  

GCOVR code coverage report [https://652.url.prtest.cppalliance.org/gcovr/index.html](https://652.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://652.url.prtest.cppalliance.org/genhtml/index.html](https://652.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://652.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://652.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2023-01-11 22:40:16 UTC  
> Url: https://github.com/boostorg/url/pull/652#issuecomment-1379575850  

GCOVR code coverage report [https://652.url.prtest.cppalliance.org/gcovr/index.html](https://652.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://652.url.prtest.cppalliance.org/genhtml/index.html](https://652.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://652.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://652.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 13

> Username: codecov[bot]  
> Created_at: 2023-01-12 05:07:29 UTC  
> Url: https://github.com/boostorg/url/pull/652#issuecomment-1379819995  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/652?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#652](https://codecov.io/gh/boostorg/url/pull/652?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (34d59c7) into [develop](https://codecov.io/gh/boostorg/url/commit/af8aa0388769fb2d9c44a9ef8919acd215739df3?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (af8aa03) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/652/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/652?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #652   +/-   ##  
========================================  
  Coverage    96.88%   96.88%             
========================================  
  Files          147      147             
  Lines         7924     7924             
========================================  
  Hits          7677     7677             
  Misses         247      247             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/652?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/652?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [af8aa03...34d59c7](https://codecov.io/gh/boostorg/url/pull/652?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 14

> Username: alandefreitas  
> Created_at: 2023-01-12 21:29:06 UTC  
> Url: https://github.com/boostorg/url/pull/652#issuecomment-1381014517  

@sdarwin Is this good to go?

---

## Comment 15

> Username: sdarwin  
> Created_at: 2023-01-12 21:55:38 UTC  
> Url: https://github.com/boostorg/url/pull/652#issuecomment-1381037293  

> @sdarwin Is this good to go  
  
Yes.    
This copy is already on the drone server.

---

## Comment 16

> Username: alandefreitas  
> Created_at: 2023-01-12 22:11:23 UTC  
> Url: https://github.com/boostorg/url/pull/652#issuecomment-1381050263  

Great. I'll implement the range thing you suggested in another PR.

---

## Comment 17

> Username: sdarwin  
> Created_at: 2023-01-12 22:46:21 UTC  
> Url: https://github.com/boostorg/url/pull/652#issuecomment-1381077007  

> the range thing you suggested in another PR.  
  
I was just trying to understand what was feasible, since those character `||` and `^` were already mentioned in the code, maybe I could already skip certain ranges or implement and/or/not logic.  However, perhaps nothing more needs to be done.  Less is more.  
Apple could support gcc in addition to clang.  The boostorg/json .drone.star has an example of apple gcc.  
Beast is having some difficulties with msvc.  It is partly because  "VARIANT": "release", needs to be set.  Could that be moved into a jam file or a drone script instead so that it doesn't need to be in `generate`.  not sure.

---
