# #45 Update PGI C++ compiler support [Closed]

> Username: dkolsen-pgi  
> Created at: 2017-07-26 20:02:51 UTC  
> Updated at: 2017-07-27 17:35:51 UTC  
> Closed at: 2017-07-27 17:35:51 UTC  
> Url: https://github.com/boostorg/type_traits/pull/45  

In the PGI C++ compiler, __float128 is a typedef of long double, not its own type.  This causes a duplicate template specialization error when defining is_floating_point<__float128>.  Change the #if expression to not define that specialization when compiling with PGI.

---

## Comment 1

> Username: Lastique  
> Created_at: 2017-07-26 21:43:36 UTC  
> Url: https://github.com/boostorg/type_traits/pull/45#issuecomment-318191627  

Maybe `BOOST_HAS_FLOAT128` should not be defined for this compiler instead?

---

## Comment 2

> Username: dkolsen-pgi  
> Created_at: 2017-07-26 22:47:53 UTC  
> Url: https://github.com/boostorg/type_traits/pull/45#issuecomment-318205043  

PGI defines __float128 to be a 128-bit IEEE floating-point type, so it makes sense for BOOST_HAS_FLOAT128 to be defined, and most uses of the macro work just fine.  But PGI defines __float128 slightly differently than GCC, so some uses of the macro don't work as expected.  
  
If this is the only place where the use of BOOST_HAS_FLOAT128 has to be special cased for PGI, then this is the correct fix.  If I run into other places that need a similar fix (which hasn't happened yet), then I will have to rethink the solution, and not defining BOOST_HAS_FLOAT128 might be the right fix.

---

## Comment 3

> Username: Lastique  
> Created_at: 2017-07-27 09:28:33 UTC  
> Url: https://github.com/boostorg/type_traits/pull/45#issuecomment-318309753  

In Boost.Config, `BOOST_HAS_FLOAT128` is documented to indicate that "The compiler has `__float128` as a native type which is distinct from all the regular C++ floating point types." If PGI defines it as a typedef then the macro should not be defined.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2017-07-27 11:48:09 UTC  
> Url: https://github.com/boostorg/type_traits/pull/45#issuecomment-318339393  

On 27/07/2017 10:28, Andrey Semashev wrote:  
>  
> In Boost.Config, |BOOST_HAS_FLOAT128| is documented to indicate that   
> "The compiler has |__float128| as a native type which is distinct from   
> all the regular C++ floating point types." If PGI defines it as a   
> typedef then the macro should not be defined.  
>  
  
I was just about to say that ;)  
  
Likewise BOOST_HAS_INT128 is only if __int128 is a distinct type.  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 5

> Username: dkolsen-pgi  
> Created_at: 2017-07-27 17:35:51 UTC  
> Url: https://github.com/boostorg/type_traits/pull/45#issuecomment-318432746  

Thanks for the information.  I should have looked that up myself.  I withdraw this pull request, and I will create a separate PR in config to not set BOOST_HAS_FLOAT128 for PGI.

---
