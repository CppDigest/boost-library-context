# #51 Build all source for library with BOOST_SERIALIZATION_SOURCE solves s… [Closed]

> Username: eldiener  
> Created at: 2017-01-21 01:33:56 UTC  
> Updated at: 2017-11-09 08:13:57 UTC  
> Closed at: 2017-11-08 17:29:26 UTC  
> Url: https://github.com/boostorg/serialization/pull/51  

…ingleton link problem. I have placed the BOOST_SERIALIZATION_SOURCE definition in the build jamfile so it applies to all the non-wide source, and removed it from each source file. Alternatively, if desired I could remove it from the jamfile and add it to each source file, but I felt the way I did it was easier. The important point to solving the singleton link problem for gcc under Windows is that the definition must apply to each source file in the build so that the build of the non-wide character serialization library sees the necessary classes/functions as exported.

---

## Comment 1

> Username: robertramey  
> Created_at: 2017-01-21 21:50:36 UTC  
> Url: https://github.com/boostorg/serialization/pull/51#issuecomment-274290575  

"Alternatively, if desired I could remove it from the jamfile and add it to each source file,"  
  
How would that be different than the way it was originally?

---

## Comment 2

> Username: eldiener  
> Created_at: 2017-01-23 13:41:52 UTC  
> Url: https://github.com/boostorg/serialization/pull/51#issuecomment-274490467  

Quoting:    
  
"Alternatively, if desired I could remove it from the jamfile and add it to each source file,"  
  
How would that be different than the way it was originally?  
  
Endquoting:  
  
It would not be different from my PR, except in following your current code to have   
#define BOOST_SERIALIZATION_SOURCE in the source files themselves.

---

## Comment 3

> Username: jeking3  
> Created_at: 2017-09-21 22:15:13 UTC  
> Url: https://github.com/boostorg/serialization/pull/51#issuecomment-331296891  

It makes sense to have this definition in the build system and not in the source files.  The headers should key off this and do the right thing for both cases.  Shouldn't the archive definition also be handled the same way?

---

## Comment 4

> Username: robertramey  
> Created_at: 2017-11-08 17:29:26 UTC  
> Url: https://github.com/boostorg/serialization/pull/51#issuecomment-342892697  

>It makes sense to have this definition in the build system and not in the source files. The headers should key off this and do the right thing for both cases. Shouldn't the archive definition also be handled the same way?  
  
I think this would conflict with the way that "visibility" works.  It's actually a nightmare as there is a lot of variation among compilers.  I don't think a "sledge hammer" approach is appropriate.

---

## Comment 5

> Username: eldiener  
> Created_at: 2017-11-09 08:13:57 UTC  
> Url: https://github.com/boostorg/serialization/pull/51#issuecomment-343078728  

I am glad you finally got serialization working with mingw(-64)/gcc on Windows. Congratulations !

---
