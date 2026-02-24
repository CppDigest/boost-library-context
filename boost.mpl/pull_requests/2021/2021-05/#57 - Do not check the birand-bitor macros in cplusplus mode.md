# #57 Do not check the birand/bitor macros in cplusplus mode [Closed]

> Username: ABBAPOH  
> Created at: 2021-05-03 20:31:22 UTC  
> Updated at: 2025-01-09 22:45:56 UTC  
> Closed at: 2025-01-03 14:06:55 UTC  
> Url: https://github.com/boostorg/mpl/pull/57  

Some 3rdparty parsers (e.g. Qt moc) fail to parse the bitand.hpp  
due to the fact that bitand is a keyword in C++, not a macro.  
Modern MSVC versions define bitand/bitor only if __cplusplus macro  
is not defined, see iso646.h: https://pastebin.com/zTcd0juT  
  
Thus, boost can also check the macro only in C mode making moc happy

---

## Comment 1

> Username: ABBAPOH  
> Created_at: 2021-05-03 20:35:51 UTC  
> Url: https://github.com/boostorg/mpl/pull/57#issuecomment-831517642  

See also: https://bugreports.qt.io/projects/QBS/issues/QBS-1632

---

## Comment 2

> Username: jeking3  
> Created_at: 2022-05-01 21:24:51 UTC  
> Url: https://github.com/boostorg/mpl/pull/57#issuecomment-1114340519  

@ABBAPOH please rebase on develop to run all tests - thanks.

---

## Comment 3

> Username: ABBAPOH  
> Created_at: 2022-08-04 06:39:21 UTC  
> Updated_at: 2022-08-22 08:21:46 UTC  
> Url: https://github.com/boostorg/mpl/pull/57#issuecomment-1204828504  

@jeking3  
Sorry for the delay, I missed your comment.  
  
Is it OK btw that the first check is only checks `#if defined(_MSC_VER)` but the second also checks __clang__ `#if defined(_MSC_VER) && !defined(__clang__)` ?

---

## Comment 4

> Username: jeking3  
> Created_at: 2025-01-03 14:06:55 UTC  
> Url: https://github.com/boostorg/mpl/pull/57#issuecomment-2569271279  

See #83

---

## Comment 5

> Username: ABBAPOH  
> Created_at: 2025-01-09 22:45:55 UTC  
> Url: https://github.com/boostorg/mpl/pull/57#issuecomment-2581387314  

Thanks!

---
