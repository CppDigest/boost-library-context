# #722 - A | A   not being simplified to A  in 1.78.0 [Closed]

> Username: allopislozano  
> Created at: 2022-04-06 08:01:43 UTC  
> Updated at: 2025-05-09 23:37:00 UTC  
> Closed at: 2025-05-09 23:36:48 UTC  
> Url: https://github.com/boostorg/spirit/issues/722  

Another functionality that has changed in  78. When having a disjunction A|A  it is no longer simplified into a single A, the synthesized attribute is variant<A,A>. Is this expected?  
  
https://godbolt.org/z/Mjoc9aKMa

---

## Comment 1

> Username: cppljevans  
> Created at: 2022-08-02 04:35:17 UTC  
> Url: https://github.com/boostorg/spirit/issues/722#issuecomment-1202006430  

Try something like the type_sequence in following in attribute _of_binary.hpp  
(of course you'll have to remove all the debug code I put in there to see what was happening):  
  
```c++  
//Purpose:  
//  Prototype a **MAYBE** solution to https://github.com/boostorg/spirit/issues/722  
//  The MAYBE is because it's unknown whether this change to type_sequence will  
//  break other parts of code such as that in alternative.hpp or detail/alternative.hpp.  
//========  
#include <boost/mp11.hpp>  
namespace mp11=boost::mp11;  
#include <boost/utility/trace_scope.hpp>  
#include <boost/iostreams/utility/templ_expr/demangle_fmt_type.hpp>  
#define USE_TRANSFER_VARIANT_UNIQUE  
  #include <boost/spirit/home/x3/support/traits/is_variant.hpp>  
  #include <boost/utility/enable_if.hpp>  
  namespace x3=boost::spirit::x3;  
    
    template <typename... T>  
    struct type_sequence  
      //A prototype of detail::type_sequence in:  
      //  attribute_of_binary.hpp  
      //which "unique-izes" the template args to boost::variant.  
      {  
        #ifdef USE_TRANSFER_VARIANT_UNIQUE  
            template   
            < template <typename...> class U  
            , typename Enable = void  
            >  
          struct maybe_variant_transfer  
            //If U is not a variant, simply transfer to U.  
            { using type=U<T...>;  
              static void trace_tmpl()  
              {  
              ; boost::trace_scope ts(stringify(":__LINE__=",__LINE__))  
              ; std::cout<<":type="<<demangle_fmt_type<type>()<<";\n";  
              ;}  
            };  
            template   
            < template <typename...> class U  
            >  
          struct maybe_variant_transfer  
            < U  
            , typename boost::enable_if  
              < typename x3::traits::is_variant  
                < U  
                  < void//anything, just to allow is_variant to work.  
                  >  
                >  
              >::type  
            >  
            //If U is a variant, assure the types are unique.  
            {   
              using mp_list_T=mp11::mp_list<T...>  
                ;  
              using unique_t=mp11::mp_unique<mp_list_T>  
                ;  
                template  
                < typename S  
                >  
              struct unpack_transfer  
                ;  
                template  
                < typename... S  
                >  
              struct unpack_transfer  
                < mp11::mp_list<S...>  
                >  
                { using type=U<S...>;  
                };  
              using type=typename unpack_transfer< unique_t>::type  
                ;  
              static void trace_tmpl()  
              {  
              ; boost::trace_scope ts(stringify(":__LINE__=",__LINE__))  
              ; std::cout<<":type="<<demangle_fmt_type<type>()<<";\n";  
              ;}  
            };  
          template <template <typename...> class U>  
          using maybe_transfer = maybe_variant_transfer<U>;  
            
          template <template <typename...> class U>  
          using transfer_to = typename maybe_transfer<U>::type;  
            
          template<template <typename...> class U>  
          static void trace_tmpl()  
          {  
          ; boost::trace_scope ts(stringify(":__LINE__=",__LINE__))  
          ; std::cout<<":transfer_to=\n"<<demangle_fmt_type<transfer_to<U>>()<<";\n"  
          ; std::cout<<":maybe_transfer<U>=\n"<<demangle_fmt_type<maybe_transfer<U>>()<<";\n"  
          ; std::cout<<":maybe_transfer<U>::trace_tmpl():\n"  
          ; maybe_transfer<U>::trace_tmpl()  
          ;}  
        #else          
          template <template <typename...> class U>  
          using transfer_to = U<T...>;  
          static void trace_tmpl()  
          {  
          ; boost::trace_scope ts(stringify(":__LINE__=",__LINE__))  
          ;}  
        #endif//USE_TRANSFER_VARIANT_UNIQUE  
        
      };//type_sequence  
        
      template   
      < typename...  
      >  
    struct not_var  
      {};  
        
int main()  
  {   
      boost::iostreams::indent_scoped_ostreambuf<char>  
    indent_outbuf(std::cout,2)  
  ; using seq=type_sequence<int,double,int>;  
  ; std::cout<<":seq=\n"<<demangle_fmt_type<seq>()<<";\n"  
  ; using xfr_var=typename seq::template transfer_to<boost::variant>;  
  ; std::cout<<":xfr_var=\n"<<demangle_fmt_type<xfr_var>()<<";\n"  
  ; std::cout<<":seq::trace_tmpl<xfr_var>():\n";  
  ; seq::trace_tmpl<boost::variant>()  
  ; using xfr_not=typename seq::template transfer_to<not_var>;  
  ; std::cout<<":xfr_not=\n"<<demangle_fmt_type<xfr_not>()<<";\n"  
  ; std::cout<<":seq::trace_tmpl<not_var>():\n";  
  ; seq::trace_tmpl<not_var>()  
  ;}  
```

---

## Comment 2

> Username: OBorce  
> Created at: 2022-09-10 20:26:11 UTC  
> Url: https://github.com/boostorg/spirit/issues/722#issuecomment-1242799948  

Any workaround for this?

---

## Comment 3

> Username: Kojoley  
> Created at: 2022-09-12 19:33:00 UTC  
> Url: https://github.com/boostorg/spirit/issues/722#issuecomment-1244244507  

Repeated types not being unified is much older thing #610, so I doubt it is the cause. Reversed order was reported in #721 though matching the order does not help this example. There is a meta issue that affects alternative parser https://github.com/boostorg/spirit/issues/707#issuecomment-1001748725 and highly likely the source of this bug too.

---

## Comment 4

> Username: eido79  
> Created at: 2022-11-03 21:09:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/722#issuecomment-1302665537  

This and #721 seem to be the consequence of the changes done in #702, combined with the issue described in #610.  
IMHO the parameter order was indeed correct, and the issue is in the definition of `is_substitute_impl` for variant types.  
  
I've implemented a prototype (crude) fix here: https://github.com/eido79/spirit/tree/variant_substitute
