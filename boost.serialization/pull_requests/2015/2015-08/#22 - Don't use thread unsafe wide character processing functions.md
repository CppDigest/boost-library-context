# #22 Don't use thread unsafe wide character processing functions. [Merged]

> Username: EdSchouten  
> Created at: 2015-08-27 14:09:44 UTC  
> Updated at: 2015-10-02 19:36:59 UTC  
> Merged at: 2015-10-02 19:29:48 UTC  
> Closed at: 2015-10-02 19:29:48 UTC  
> Url: https://github.com/boostorg/serialization/pull/22  

The mbtowc(), wctomb() and mblen() functions are not supposed to be  
thread-safe, as they have to keep parsing state in a global variable.  
The mbrtowc(), wcrtomb() and mbrlen() functions should be used instead.  
  
This change also simplifies how wchar_from_mb<Base>::drain() works.  
There is no need to first iterate over the input using mblen().  
mbrtowc() can store the partially parsed multibyte character in its  
mbstate_t.

---

## Comment 1

> Username: robertramey  
> Created_at: 2015-09-29 07:10:16 UTC  
> Url: https://github.com/boostorg/serialization/pull/22#issuecomment-143968551  

I've got a slight problem with this.  
  
What if BOOST_NO_CWCHAR is defined?  Then what.  Shouldn't there be an else in there to support compilers which don't have a <cwchar> header?

---

## Comment 2

> Username: EdSchouten  
> Created_at: 2015-09-29 08:12:14 UTC  
> Url: https://github.com/boostorg/serialization/pull/22#issuecomment-143980908  

Hi Robert,  
  
For `xml_iarchive_impl.ipp` this change actually does the right thing: we used to test against `BOOST_NO_CWCHAR`, but then used the non-reentrant versions provided by `<cstdlib>` instead. It now actually uses the `<cwchar>` functions.  
  
For the other files the question that I have is: are there actually any operating systems out there that support `mbtowc()`, but not `mbrtowc()` (and that Boost also wants to support)? If so, we could add back the old code and test against `BOOST_NO_CWCHAR` if you want.

---

## Comment 3

> Username: robertramey  
> Created_at: 2015-09-29 20:54:04 UTC  
> Url: https://github.com/boostorg/serialization/pull/22#issuecomment-144188498  

"For the other files the question that I have is: are there actually any operating systems out there that support mbtowc(), but not mbrtowc() (and that Boost also wants to support)?"  
  
The short answer is: I have no idea.  Bitter experience has taught me not to second guess what other players, (compiler developers, standard library writers, and users) are going to do.  It's actually easier just to include the wider compatibility.  It might not seem so, but almost all configurations have quirks of some sort so this is the only strategy that really works.  When one runs across these situations, it's a huge amount of time for everyone involved to research the circumstances then make a fix.  
  
 If so, we could add back the old code and test against BOOST_NO_CWCHAR if you want."  
  
I think that would be the best, I would appreciate your doing this.  
  
Thanks for you help.  
  
Robert Ramey

---

## Comment 4

> Username: robertramey  
> Created_at: 2015-10-02 19:36:59 UTC  
> Url: https://github.com/boostorg/serialization/pull/22#issuecomment-145135115  

Hmm - I merged this in, pulled in the changes and run the tests and all the xml_warchive tests failed.  Did you actually test this?  I'm using Clang on Mac OS X

---
