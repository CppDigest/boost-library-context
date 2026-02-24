# #68 Remove dependence on <typeinfo> header [Merged]

> Username: kkaefer  
> Created at: 2018-10-09 14:10:44 UTC  
> Updated at: 2018-11-03 13:20:44 UTC  
> Merged at: 2018-11-03 13:20:44 UTC  
> Closed at: 2018-11-03 13:20:44 UTC  
> Url: https://github.com/boostorg/iostreams/pull/68  

Using `typeid` requires compilation with RTTI. Since iostreams is used in many other boost modules, this effectively disables using `-fno-rtti` with those upstream modules too.

---

## Comment 1

> Username: jeking3  
> Created_at: 2018-10-09 15:52:10 UTC  
> Updated_at: 2018-10-09 15:52:47 UTC  
> Url: https://github.com/boostorg/iostreams/pull/68#issuecomment-428247042  

This is a public interface change (but possibly not in a publicly exposed class, I'll look into it).  Let's see how the build goes and then may need to run a full boost build with tests locally to ensure this change doesn't break other boost libraries.  I also didn't check to see if the documentation is affected or not.

---

## Comment 2

> Username: mclow  
> Created_at: 2018-10-09 16:15:37 UTC  
> Url: https://github.com/boostorg/iostreams/pull/68#issuecomment-428255583  

> Since iostreams is used in many other boost modules  
  
Is it? Searching for `/iostreams/` gave me lots of hits, but almost all of them were in Boost.IOStreams.  
  
The only places I could find that it was *used* were Boost.Spirit, and Quickbook.  
It is used in a couple other library's tests - Boost.Contract and Boost.Process.  
It's mentioned in the Boost.Locale docs, and the Boost.Log docs, but not used there.

---

## Comment 3

> Username: kkaefer  
> Created_at: 2018-10-09 16:19:01 UTC  
> Url: https://github.com/boostorg/iostreams/pull/68#issuecomment-428256773  

Ok, so it's used in two other modules; that doesn't change anything about this patch though 😅

---

## Comment 4

> Username: mclow  
> Created_at: 2018-10-09 16:27:21 UTC  
> Url: https://github.com/boostorg/iostreams/pull/68#issuecomment-428259601  

I agree - it doesn't change anything about this patch. It does affect the motivation, though.

---

## Comment 5

> Username: pdimov  
> Created_at: 2018-10-09 16:55:38 UTC  
> Url: https://github.com/boostorg/iostreams/pull/68#issuecomment-428268957  

> This is a public interface change  
  
It's not, see  
  
https://github.com/boostorg/core/blob/1d9d6f579e794743689d19da9563385ad2cd0caf/include/boost/core/typeinfo.hpp#L134

---

## Comment 6

> Username: pdimov  
> Created_at: 2018-10-09 16:57:36 UTC  
> Url: https://github.com/boostorg/iostreams/pull/68#issuecomment-428269635  

For the UBsan run, you have to use `visibility=global`.

---

## Comment 7

> Username: jeking3  
> Created_at: 2018-10-09 17:17:22 UTC  
> Url: https://github.com/boostorg/iostreams/pull/68#issuecomment-428276219  

@pdimov Darn, that means every .travis.yml has to change.

---

## Comment 8

> Username: pdimov  
> Created_at: 2018-10-09 17:24:31 UTC  
> Url: https://github.com/boostorg/iostreams/pull/68#issuecomment-428278648  

Yeah, it's an annoying issue with UBsan and hidden visibility.

---

## Comment 9

> Username: jeking3  
> Created_at: 2018-10-09 19:20:12 UTC  
> Url: https://github.com/boostorg/iostreams/pull/68#issuecomment-428317698  

Please rebase on develop to get the UBSAN issue cleared out for your CI validation.

---

## Comment 10

> Username: codecov[bot]  
> Created_at: 2018-10-10 11:04:31 UTC  
> Updated_at: 2018-10-10 13:26:15 UTC  
> Url: https://github.com/boostorg/iostreams/pull/68#issuecomment-428531184  

# [Codecov](https://codecov.io/gh/boostorg/iostreams/pull/68?src=pr&el=h1) Report  
> Merging [#68](https://codecov.io/gh/boostorg/iostreams/pull/68?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/iostreams/commit/8c8069bd28bad9eb815bfea313013d0de65d0faa?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `60%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/iostreams/pull/68/graphs/tree.svg?width=650&token=LBEfwtNfca&height=150&src=pr)](https://codecov.io/gh/boostorg/iostreams/pull/68?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #68   +/-   ##  
========================================  
  Coverage    64.73%   64.73%             
========================================  
  Files           81       81             
  Lines         3547     3547             
  Branches       950      950             
========================================  
  Hits          2296     2296             
  Misses         461      461             
  Partials       790      790  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/iostreams/pull/68?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [...st/iostreams/detail/streambuf/linked\_streambuf.hpp](https://codecov.io/gh/boostorg/iostreams/pull/68/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9pb3N0cmVhbXMvZGV0YWlsL3N0cmVhbWJ1Zi9saW5rZWRfc3RyZWFtYnVmLmhwcA==) | `94.11% <ø> (ø)` | :arrow_up: |  
| [...st/iostreams/detail/streambuf/direct\_streambuf.hpp](https://codecov.io/gh/boostorg/iostreams/pull/68/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9pb3N0cmVhbXMvZGV0YWlsL3N0cmVhbWJ1Zi9kaXJlY3Rfc3RyZWFtYnVmLmhwcA==) | `51.66% <0%> (ø)` | :arrow_up: |  
| [.../iostreams/detail/streambuf/indirect\_streambuf.hpp](https://codecov.io/gh/boostorg/iostreams/pull/68/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9pb3N0cmVhbXMvZGV0YWlsL3N0cmVhbWJ1Zi9pbmRpcmVjdF9zdHJlYW1idWYuaHBw) | `65% <100%> (ø)` | :arrow_up: |  
| [include/boost/iostreams/chain.hpp](https://codecov.io/gh/boostorg/iostreams/pull/68/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9pb3N0cmVhbXMvY2hhaW4uaHBw) | `62.58% <66.66%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/iostreams/pull/68?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/iostreams/pull/68?src=pr&el=footer). Last update [8c8069b...756b9d0](https://codecov.io/gh/boostorg/iostreams/pull/68?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
