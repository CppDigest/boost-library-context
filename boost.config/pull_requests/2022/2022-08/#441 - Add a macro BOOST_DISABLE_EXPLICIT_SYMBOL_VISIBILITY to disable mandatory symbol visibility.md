# #441 Add a macro BOOST_DISABLE_EXPLICIT_SYMBOL_VISIBILITY to disable mandatory symbol visibility [Merged]

> Username: jcelerier  
> Created at: 2022-08-02 09:26:33 UTC  
> Updated at: 2022-09-17 17:17:10 UTC  
> Merged at: 2022-09-17 15:24:50 UTC  
> Closed at: 2022-09-17 15:24:50 UTC  
> Url: https://github.com/boostorg/config/pull/441  



---

## Comment 1

> Username: nigels-com  
> Created_at: 2022-08-02 11:10:33 UTC  
> Url: https://github.com/boostorg/config/pull/441#issuecomment-1202343280  

We'd use that, for sure.  +1

---

## Comment 2

> Username: mardigontoler  
> Created_at: 2022-09-12 16:34:56 UTC  
> Url: https://github.com/boostorg/config/pull/441#issuecomment-1243996436  

This would be useful for me, as well.

---

## Comment 3

> Username: jcelerier  
> Created_at: 2022-09-12 17:00:21 UTC  
> Url: https://github.com/boostorg/config/pull/441#issuecomment-1244028573  

any possible review ? @pdimov @jzmaddock

---

## Comment 4

> Username: pdimov  
> Created_at: 2022-09-12 17:07:55 UTC  
> Url: https://github.com/boostorg/config/pull/441#issuecomment-1244036476  

Should probably be done in suffix.hpp once; if defined, undefine the other macros and define them as empty. Here for instance: https://github.com/boostorg/config/blob/1cff5e37bb69f42ffab73438de61e34c71258f19/include/boost/config/detail/suffix.hpp#L40

---

## Comment 5

> Username: jcelerier  
> Created_at: 2022-09-12 17:30:16 UTC  
> Url: https://github.com/boostorg/config/pull/441#issuecomment-1244068615  

done

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2022-09-17 12:36:49 UTC  
> Url: https://github.com/boostorg/config/pull/441#issuecomment-1250063872  

Looks good to me, merging...

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2022-09-17 12:39:23 UTC  
> Url: https://github.com/boostorg/config/pull/441#issuecomment-1250064265  

Ah, actually I see this breaks the documentation build, looks like there's a ']' missing.

---

## Comment 8

> Username: Kojoley  
> Created_at: 2022-09-17 16:08:02 UTC  
> Url: https://github.com/boostorg/config/pull/441#issuecomment-1250097208  

Wouldn't linker strip out all the symbols and leave you with an empty static library?

---

## Comment 9

> Username: jcelerier  
> Created_at: 2022-09-17 17:05:55 UTC  
> Updated_at: 2022-09-17 17:06:50 UTC  
> Url: https://github.com/boostorg/config/pull/441#issuecomment-1250106456  

@Kojoley no, why would it ? static libraries contain all the (non-static/anonymous namespace) symbols. actually it doesn't really make sense as a question: the linker isn't involved in making static libraries, they are just an archive of the .o's

---

## Comment 10

> Username: Kojoley  
> Created_at: 2022-09-17 17:12:12 UTC  
> Url: https://github.com/boostorg/config/pull/441#issuecomment-1250107683  

> @Kojoley no, why would it ? static libraries contain all the (non-static/anonymous namespace) symbols. actually it doesn't really make sense as a question: the linker isn't involved in making static libraries,  
  
https://gcc.gnu.org/onlinedocs/gnat_ugn/Compilation-options.html  
  
> they are just an archive of the .o's  
  
No, archives and static libraries are different things https://softwareengineering.stackexchange.com/questions/389383/what-is-the-difference-between-a-static-library-and-an-archive-library

---

## Comment 11

> Username: jcelerier  
> Created_at: 2022-09-17 17:13:45 UTC  
> Updated_at: 2022-09-17 17:17:10 UTC  
> Url: https://github.com/boostorg/config/pull/441#issuecomment-1250107913  

.. the stackoverflow link you pasted literally says "A static library is just an ar archive (like zip but ancient) of object files.". Also you pasted the GNAT documentation (Ada) ; I don't know Ada so maybe it has different semantics.  
  
But I can promise that if you do :  
  
  
     $ echo "void foo() { }" > foo.cpp  
     $ g++ -c foo.cpp -o foo.o   
     $ ar rcs libfoo.a foo.o  
  
you'll have a _Z3foo symbol available in libfoo.a  (and that at no point the linker has been invoked)

---
