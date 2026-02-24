# #19 Changes to fix serialization for gcc and clang on Windows [Closed]

> Username: eldiener  
> Created at: 2015-07-31 10:56:16 UTC  
> Updated at: 2015-10-04 18:16:38 UTC  
> Closed at: 2015-10-04 18:16:38 UTC  
> Url: https://github.com/boostorg/serialization/pull/19  

These are changes which will fix the building of the serialization library for gcc and clang on Windows. There are still problems building the wide character serialization library for gcc and clang on Windows, but this problem appears to be one of the 'ld' linker involved and so far the mingw(-64) people are not interested in fixing this problem so I am not going to bother trying to get the building of the wide character serialization library on Windows to work properly until they fix 'ld' on Windows.

---

## Comment 1

> Username: eldiener  
> Created_at: 2015-08-02 22:03:35 UTC  
> Url: https://github.com/boostorg/serialization/pull/19#issuecomment-127076086  

I was able to fix the wide character build of the serialization library for mingw-64/gcc/ld on Windows by providing a copy constructor and a destructor for the xml_archive_exception class. The copy constructor is needed because functionality in the wide character serialization library throws objects of xml_archive_exception by value and the destructor, which does nothing, is needed because gcc needs every implicitly defined class member functiion to be explicitly exported/imported if the class is being created and destroyed outside the shared library in which it is defined.

---

## Comment 2

> Username: eldiener  
> Created_at: 2015-08-11 02:31:18 UTC  
> Url: https://github.com/boostorg/serialization/pull/19#issuecomment-129682671  

Fixes in the clang trunk for clang 3.8 allows the build of the serialization library to work for clang on Windows targeting mingw(-64)/gcc. Unfortunately clang versions 3.4.1, 3.5.2, and 3.6.2, all on Windows, will not build due to the bug which was only recently fixed on the trunk. It is highly doubtful that this bug fix will be backported to earlier versions. The clang 3.7 version has been closed but has not yet been officially released, but again I do not expect a backport of the recent fix. This is a problem of course with these releases on Windows, not on Linux.  
  
For clang 3.6.2 and 3.5.2 on Windows the normal serialization library will build but the wide character serialization library will not build. For clang 3.4.1 on Windows nothing builds with numerous errors, and I believe the export/import of individual member functions is just broken for clang 3.4.1 on Windows and will not work.

---

## Comment 3

> Username: xantares  
> Created_at: 2015-08-18 12:42:17 UTC  
> Url: https://github.com/boostorg/serialization/pull/19#issuecomment-132194822  

:+1: it does fix this 1.59 regression with mingw64/gcc !  
  
the only other lib failling with mingw now is context(+coroutine*) after the 1.58 regression (https://github.com/boostorg/context/issues/16)

---

## Comment 4

> Username: eldiener  
> Created_at: 2015-10-04 18:16:38 UTC  
> Url: https://github.com/boostorg/serialization/pull/19#issuecomment-145370712  

I see that the latest 'develop' branch for serialization now works with mingw/gcc so i will close this PR.

---
