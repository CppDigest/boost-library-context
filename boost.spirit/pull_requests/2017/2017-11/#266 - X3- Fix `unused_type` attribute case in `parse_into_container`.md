# #266 X3: Fix `unused_type` attribute case in `parse_into_container` [Merged]

> Username: Kojoley  
> Created at: 2017-11-08 16:47:36 UTC  
> Updated at: 2017-11-09 20:22:37 UTC  
> Merged at: 2017-11-08 22:24:07 UTC  
> Closed at: 2017-11-08 22:24:07 UTC  
> Url: https://github.com/boostorg/spirit/pull/266  

Passing `unused_type` attribute to `parse_into_container_impl::call` produces a compilation error (clang: https://travis-ci.org/Kojoley/spirit/jobs/298783103 gcc: https://travis-ci.org/Kojoley/spirit/jobs/298783104).  
  
The issue was introduced in 379413a50c93539aa432fa9f9fc94fec1d6aa60d.  
  
While the fix restores x3 test suite (checked on msvc, gcc, clang) I am not quite sure it is the right solution. @djowel please, review the fix.

---

## Comment 1

> Username: djowel  
> Created_at: 2017-11-08 21:56:06 UTC  
> Url: https://github.com/boostorg/spirit/pull/266#issuecomment-342973762  

Yes, you are doing it right. A "fix" without a test case is always suspicious.

---

## Comment 2

> Username: Kojoley  
> Created_at: 2017-11-08 22:06:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/266#issuecomment-342976821  

Without this fix current test suite does not pass (compile error in `confix` test, see logs above). You want me to come up with a new one?

---

## Comment 3

> Username: djowel  
> Created_at: 2017-11-08 22:17:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/266#issuecomment-342979651  

What "new one" do you have in mind?

---

## Comment 4

> Username: djowel  
> Created_at: 2017-11-08 22:23:43 UTC  
> Url: https://github.com/boostorg/spirit/pull/266#issuecomment-342981237  

Anyway, I think this is correct as far as I can tell.

---

## Comment 5

> Username: Kojoley  
> Created_at: 2017-11-08 22:37:58 UTC  
> Url: https://github.com/boostorg/spirit/pull/266#issuecomment-342984801  

That does not exist (or disabled) in current test suite and targets particular problem.

---
