# #91 - Named template parameters are completely broken in Boost 1.71 [Open]

> Username: svgpp  
> Created at: 2019-08-22 21:35:27 UTC  
> Updated at: 2020-05-19 11:17:56 UTC  
> Url: https://github.com/boostorg/parameter/issues/91  

Since Boost 1.71 following code fails to compile on VS 2017 - default type is chosen instead of passed one and static assert fires.  
  
```  
#include <boost/parameter/parameters.hpp>  
#include <boost/parameter/name.hpp>  
  
BOOST_PARAMETER_TEMPLATE_KEYWORD(type_param)  
BOOST_PARAMETER_TEMPLATE_KEYWORD(another_type_param)  
  
class A {};  
  
class B {};  
  
class C {};  
  
template<class... Args>  
class CheckParams {  
public:  
  typedef typename boost::parameter::parameters<  
    boost::parameter::optional<tag::type_param>  
  >::bind<Args...>::type args;  
  typedef typename boost::parameter::value_type<args, tag::type_param,  
    B>::type basic_shapes_policy;  
  
  CheckParams() {  
    static_assert(std::is_same<basic_shapes_policy, A>::value, "");  
  }  
};  
  
void test() {  
  CheckParams<  
      another_type_param<C>,  
      type_param<A>  
    > instance;  
}  
```

---

## Comment 1

> Username: CromwellEnage  
> Created at: 2019-09-05 04:35:17 UTC  
> Url: https://github.com/boostorg/parameter/issues/91#issuecomment-528192939  

Sorry for the delay, I'll take a look at this ASAP.

---

## Comment 2

> Username: fhw72  
> Created at: 2019-10-04 12:24:12 UTC  
> Url: https://github.com/boostorg/parameter/issues/91#issuecomment-538374837  

Any update (or roadmap) on this issue?

---

## Comment 3

> Username: prudhomm  
> Created at: 2019-10-09 07:05:59 UTC  
> Updated at: 2019-10-09 07:06:44 UTC  
> Url: https://github.com/boostorg/parameter/issues/91#issuecomment-539868506  

I agree.  The changes in 1.71 broke our code.  
I cannot have this kind of parameter:  
```  
( pattern,          *( boost::is_integral<mpl::_> ), Pattern::COUPLED )  
```  
the solution is to write this  
```  
( pattern,          ( int), Pattern::COUPLED )  
```  
  
see feelpp/feelpp#1373 for more details

---

## Comment 4

> Username: PlasmaHH  
> Created at: 2019-11-21 14:42:45 UTC  
> Url: https://github.com/boostorg/parameter/issues/91#issuecomment-557115231  

Probably related having  
`( fd , (int), -1 )`  
previously resulted in an `int` parameter, now its a `const int` due to taking the default. Same for `( xxx,(const sometype*),nullptr)` turning out to be `nullptr_t` instead of `const sometype*`

---

## Comment 5

> Username: svgpp  
> Created at: 2020-01-14 20:05:11 UTC  
> Url: https://github.com/boostorg/parameter/issues/91#issuecomment-574349022  

I did some investigation and found out that `make_parameter_spec_items` in 1.71 changed behavior compared to `BOOST_PARAMETER_build_arg_list` from 1.70, in 1.70 number of bound args (`::bind<...>`) may be greater than the count of parameter definitions (`parameters<...>`). In 1.71 smaller of the two is used and remaining is dropped.

---

## Comment 6

> Username: PlasmaHH  
> Created at: 2020-05-13 11:26:04 UTC  
> Url: https://github.com/boostorg/parameter/issues/91#issuecomment-627920085  

Is there any update on this? Being kinda stuck between a rock and a hard place, have tried with boost 1.73 to no avail, but would like to upgrade to gcc 10 which doesn't play well with 1.70 and c++20 settings ...

---

## Comment 7

> Username: PlasmaHH  
> Created at: 2020-05-19 11:17:56 UTC  
> Url: https://github.com/boostorg/parameter/issues/91#issuecomment-630754055  

Just got the time to fix it at least for my use cases, I went a probably unusual route: I made the final dispatch layer function have the signature as required by the macro calls tuples. Digging through all the code it looks like the current behaviour was intended: Accept parameters that are convertible, but retain their type as much as possible. This doesn't work out well when you expect the macro to act like for a normal function declaration. Maybe it could be taken into consideration to have two modes of operation here (given the size of my patch, probably not too bad an option), one using perfect forwarding of everything, the other to make the function have truly the requested signature, just as an idea... (and yes, the patch is a messy proof of concept)  
  
[fix_parameters.txt](https://github.com/boostorg/parameter/files/4649975/fix_parameters.txt)
