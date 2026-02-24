# #25 Replace invalid characters by 0xFFFD [Closed]

> Username: Flamefire  
> Created at: 2017-09-29 22:00:07 UTC  
> Updated at: 2019-12-13 18:08:39 UTC  
> Closed at: 2019-12-13 18:08:34 UTC  
> Url: https://github.com/boostorg/nowide/pull/25  

Closes #16  
  
This adds a conversion method parameter so the old behaviour can still be used.  
  
Only issue: The std::(w)string overloads for `widen` and `narrow` use the boost conversions which can either throw an error or skip invalid chars. Not sure what to do with them.

---

## Comment 1

> Username: Flamefire  
> Created_at: 2017-10-03 10:29:48 UTC  
> Url: https://github.com/boostorg/nowide/pull/25#issuecomment-333802352  

I'll add another commit to this PR to throw an exception if `args` can't find the commandline (win32 function fails) I'll also do the same to the environment variable getting as requested in the issue. However I don't see a way to notify the user about the replacement.

---

## Comment 2

> Username: artyom-beilis  
> Created_at: 2017-10-03 11:02:08 UTC  
> Url: https://github.com/boostorg/nowide/pull/25#issuecomment-333808961  

I just want to notice that FFFD issue is little bit more complicated.  
  
- I need to update streambuf as well for nowide cin/cout/cerr  
- I want to add character replacement feature not via Nowide but rather add it to Boost.Locale (I maintain) and than update nowide since it is more correct way to do things like this.  
- I need to do some API/doc updates since most functions do not throw/return error codes.  
  
So I'm not really sure I can merge it as is.

---

## Comment 3

> Username: Flamefire  
> Created_at: 2017-10-03 22:38:29 UTC  
> Url: https://github.com/boostorg/nowide/pull/25#issuecomment-333999438  

Well there are (for some reason) 2 conversions: One uses Boost.Locale (widen/narrow) and the other uses Boost.Nowide (the one I changed). What was the reason for having both?  
  
Yeah I think `iostreams.cpp` needs to be changed but that looks minor and could be done in a very similar way. The docu needs updating of course, but I did not want to put to much time in it, if the functionality itself is not right yet. But this PR solves most of the problems in an easy way. Updating Boost.Locale would mean that a (much) higher boost version will be required which will be a problem for some projects (e.g. we need to stick to 1.55)  
  
For reference there is a great C++03 UTF8 library which also does the replacements in a sensible way: https://github.com/nemtrif/utfcpp  
  
Lastly: Boost.Locale provides a `method` enum which could be easily extended.

---

## Comment 4

> Username: Flamefire  
> Created_at: 2019-12-13 18:08:34 UTC  
> Url: https://github.com/boostorg/nowide/pull/25#issuecomment-565546747  

Done by https://github.com/boostorg/nowide/commit/11a7d83c1c9ae1bca57ca2be0c5f062946ba517c

---
