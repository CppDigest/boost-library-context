# #1791 Properly handle filename encodings in file_win32 [Closed]

> Username: mika-fischer  
> Created at: 2019-12-23 19:24:54 UTC  
> Updated at: 2019-12-29 19:20:48 UTC  
> Closed at: 2019-12-29 19:20:48 UTC  
> Url: https://github.com/boostorg/beast/pull/1791  

This brings file_win32 in sync with the documentation.  
Previously, the path passed to open worked if encoded in the system  
codepage (which is almost never UTF-8).  
Now, the path must be encoded as UTF-8, as stated in the  
documentation.  
  
Adapt file tests so that for file_win32 all paths include a unicorn  
character.  
  
Note that file_stdio on Windows is still broken as before.  
  
Partially fixes #793

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2019-12-23 20:41:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1791#pullrequestreview-336004254  

📁 include/boost/beast/core/detail/win32_unicode_path.hpp

```diff
  56 |+     WCHAR_ const* c_str() const noexcept
  57 |+     {
  58 |+         return dynamic_buf_.empty()
```

> Username: vinniefalco  
> Created_at: 2019-12-23 20:41:40 UTC  
> Url: https://github.com/boostorg/beast/pull/1791#discussion_r360997181  

Oh I like this! Avoid dynamic allocation for the 99% of cases :)


---

## Review 2 [Approved]

> Username: Flamefire  
> Created_at: 2019-12-23 20:44:59 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/beast/pull/1791#pullrequestreview-336002595  

Some minor things, besides that lgtm

📁 include/boost/beast/core/detail/win32_unicode_path.hpp

```diff
  15 |+ #include <boost/beast/core/error.hpp>
  16 |+ #include <boost/winapi/character_code_conversion.hpp>
  17 |+ #include <boost/winapi/file_management.hpp>
```

> Username: Flamefire  
> Created_at: 2019-12-23 20:34:40 UTC  
> Updated_at: 2019-12-23 20:44:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1791#discussion_r360995851  

unused header

---

📁 include/boost/beast/core/detail/win32_unicode_path.hpp

```diff
  49 |+                 ec.assign(boost::winapi::GetLastError(),
  50 |+                     system_category());
  51 |+                 return;
```

> Username: Flamefire  
> Created_at: 2019-12-23 20:35:45 UTC  
> Updated_at: 2019-12-23 20:44:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1791#discussion_r360996067  

return not required?

> Username: vinniefalco  
> Created_at: 2019-12-23 20:48:06 UTC  
> Url: https://github.com/boostorg/beast/pull/1791#discussion_r360998437  

I prefer it for consistency as it makes the code coverage more readable. Otherwise if it falls through then the coverage report could look like both branches were taken when they weren't.


📁 include/boost/beast/core/impl/file_win32.ipp

```diff
  19 | #include <boost/winapi/access_rights.hpp>
  20 | #include <boost/winapi/error_codes.hpp>
  20 |- #include <boost/winapi/file_management.hpp>
```

> Username: Flamefire  
> Created_at: 2019-12-23 20:38:11 UTC  
> Updated_at: 2019-12-23 20:44:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1791#discussion_r360996525  

this include should be here

> Username: mika-fischer  
> Created_at: 2019-12-23 21:22:10 UTC  
> Url: https://github.com/boostorg/beast/pull/1791#discussion_r361004935  

Yes, sorry, that got moved accidentally...


---

## Comment 3

> Username: pdimov  
> Created_at: 2019-12-23 20:47:26 UTC  
> Url: https://github.com/boostorg/beast/pull/1791#issuecomment-568577942  

It depends on MS's C library. BOOST_MSVC is a reasonable condition, although clang-cl (and intel-win) is omitted. Checking _MSC_VER (set by all compilers masquerading as MSVC) instead is an option.

---

## Comment 4

> Username: Flamefire  
> Created_at: 2019-12-23 21:02:20 UTC  
> Url: https://github.com/boostorg/beast/pull/1791#issuecomment-568580647  

> Why BOOST_MSVC here but _WIN32 in the other file?  
  
Good point. While the `_wfopen` is a MS extension it is supported by all(?) windows targeting compilers. BoostNowide relies on that. But I'm not sure about `_wfopen_s`.  
  
So IMO `file_stdio` should use WIN32 to and maybe additionally `#ifdef` to use `_wfopen_s` or `_wfopen`

---

## Comment 5

> Username: mika-fischer  
> Created_at: 2019-12-23 21:18:03 UTC  
> Url: https://github.com/boostorg/beast/pull/1791#issuecomment-568583769  

Even before these changes, the switch between `fopen` and `fopen_s` was done based on `BOOST_MSVC`, so I just left it as-is. I actually don't know under which conditions `_wfopen` and `_wfopen_s` is available...  
  
In any case, I'll be mostly offline until end of Januar, so please feel free to amend this pull request as you see fit.

---

## Comment 6

> Username: codecov[bot]  
> Created_at: 2019-12-23 22:17:54 UTC  
> Updated_at: 2019-12-23 22:18:14 UTC  
> Url: https://github.com/boostorg/beast/pull/1791#issuecomment-568594491  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1791?src=pr&el=h1) Report  
> Merging [#1791](https://codecov.io/gh/boostorg/beast/pull/1791?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/9be923c40e09cef97d23a3b93161458ade0280b8?src=pr&el=desc) will **increase** coverage by `<.01%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1791/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1791?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1791      +/-   ##  
===========================================  
+ Coverage    95.16%   95.17%   +<.01%       
===========================================  
  Files          156      156                
  Lines        11949    11949                
===========================================  
+ Hits         11371    11372       +1       
+ Misses         578      577       -1  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1791?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/impl/file\_stdio.ipp](https://codecov.io/gh/boostorg/beast/pull/1791/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvZmlsZV9zdGRpby5pcHA=) | `84.29% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/zlib/detail/inflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1791/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9pbmZsYXRlX3N0cmVhbS5pcHA=) | `94.57% <0%> (+0.19%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1791?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1791?src=pr&el=footer). Last update [9be923c...8ecb9c5](https://codecov.io/gh/boostorg/beast/pull/1791?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 7 [Approved]

> Username: madmongo1  
> Created_at: 2019-12-24 00:28:37 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/beast/pull/1791#pullrequestreview-336045729  

Other than the one previously mentioned missing include, I have found:  
  
* no logic errors  
* no dangerous constructs  
* no stylistic concerns  
* no inconsistencies  
  
Recommend: Approve

---

## Comment 8

> Username: vinniefalco  
> Created_at: 2019-12-24 00:46:31 UTC  
> Url: https://github.com/boostorg/beast/pull/1791#issuecomment-568615478  

There are compilation errors on some configurations:  
https://travis-ci.org/boostorg/beast

---

## Comment 9

> Username: madmongo1  
> Created_at: 2019-12-24 00:56:22 UTC  
> Url: https://github.com/boostorg/beast/pull/1791#issuecomment-568616576  

Ugh!  
  
Narrowing conversion on signed chars. Only in clang.  
  
libs/beast/test/beast/core/file_test.hpp:43:66: note: insert an  
explicit cast to silence this issue  
1027    char                  unicode_suffix[] = { 0xf0, 0x9f, 0xa6,  
0x84, 0x00 }; // UTF-8 unicorn  
1028  
  
  
On Tue, 24 Dec 2019 at 01:46, Vinnie Falco <notifications@github.com> wrote:  
  
> There are compilation errors on some configurations:  
> https://travis-ci.org/boostorg/beast  
>  
> —  
> You are receiving this because your review was requested.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/pull/1791?email_source=notifications&email_token=ABHOZSIQFLJO7ELMXBWZKWDQ2FLWRA5CNFSM4J6XJX7KYY3PNVWWK3TUL52HS4DFVREXG43VMVBW63LNMVXHJKTDN5WW2ZLOORPWSZGOEHSGENQ#issuecomment-568615478>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABHOZSILYWC7T64NIPKZ3SLQ2FLWRANCNFSM4J6XJX7A>  
> .  
>  
--   
Richard Hodges  
hodges.r@gmail.com  
office: +442032898513  
home: +376841522  
mobile: +376380212

---

## Comment 10

> Username: madmongo1  
> Created_at: 2019-12-24 09:42:21 UTC  
> Url: https://github.com/boostorg/beast/pull/1791#issuecomment-568708383  

Mea culpa not catching the clang error. Have updated my local workflow to build all standards on both gcc and clang.

---

## Comment 11

> Username: madmongo1  
> Created_at: 2019-12-24 10:36:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1791#issuecomment-568719725  

Here's a patch which fixes the clang compilation issue.  
  
I didn't know whether it was permissible to simply push to the PR branch.  
  
```  
diff --git a/test/beast/core/file_test.hpp b/test/beast/core/file_test.hpp  
index 82565e170..280bf7276 100644  
--- a/test/beast/core/file_test.hpp  
+++ b/test/beast/core/file_test.hpp  
@@ -40,7 +40,7 @@ test_file()  
 #ifdef _WIN32  
     boost::winapi::WCHAR_ unicode_suffix[] = { 0xd83e, 0xdd84, 0x0000 }; // UTF-16-LE unicorn  
 #else  
-    char                  unicode_suffix[] = { 0xf0, 0x9f, 0xa6, 0x84, 0x00 }; // UTF-8 unicorn  
+    char                  unicode_suffix[] = { '\xf0', '\x9f', '\xa6', '\x84', '\x00' }; // UTF-8 unicorn  
 #endif  
   
     class temp_path  
```  
  
I have a commit that shows the result of merging the PR into `develop` and then applying the fix:  
  
https://github.com/madmongo1/beast/commit/04581e1028e6e6b1bffc45a79de445a62a02595d

---

## Comment 12

> Username: Flamefire  
> Created_at: 2019-12-24 17:06:27 UTC  
> Url: https://github.com/boostorg/beast/pull/1791#issuecomment-568779561  

How about `const char* unicode_suffix = "\xf0\x9f\xa6\x84"; // UTF-8 unicorn` which is less clumsy

---

## Review 13 [Commented]

> Username: Flamefire  
> Created_at: 2019-12-24 17:07:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1791#pullrequestreview-336247967  

📁 test/beast/core/file_test.hpp

```diff
  41 |+     boost::winapi::WCHAR_ unicode_suffix[] = { 0xd83e, 0xdd84, 0x0000 }; // UTF-16-LE unicorn
  42 |+ #else
  43 |+     char                  unicode_suffix[] = { 0xf0, 0x9f, 0xa6, 0x84, 0x00 }; // UTF-8 unicorn
```

> Username: Flamefire  
> Created_at: 2019-12-24 17:06:59 UTC  
> Updated_at: 2019-12-24 17:07:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1791#discussion_r361202080  

```diff  
-    char                  unicode_suffix[] = { 0xf0, 0x9f, 0xa6, 0x84, 0x00 }; // UTF-8 unicorn  
+    const char* unicode_suffix = "\xf0\x9f\xa6\x84"; // UTF-8 unicorn  
```

> Username: madmongo1  
> Created_at: 2019-12-24 18:00:49 UTC  
> Url: https://github.com/boostorg/beast/pull/1791#discussion_r361208087  

This would also solve the narrowing error in clang.  
This PR is now superceded by PR1793, in which I fixed the error by using char literals.  
Not as elegant as this solution, but I wanted to make the smallest change possible.


---
