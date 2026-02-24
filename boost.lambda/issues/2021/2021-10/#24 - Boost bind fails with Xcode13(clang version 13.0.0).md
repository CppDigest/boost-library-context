# #24 - Boost bind fails with Xcode13(clang version 13.0.0) [Closed]

> Username: kkumar45  
> Created at: 2021-10-13 08:25:20 UTC  
> Updated at: 2021-12-29 17:28:09 UTC  
> Closed at: 2021-12-26 22:14:39 UTC  
> Url: https://github.com/boostorg/lambda/issues/24  

I am seeing failures when using `boost::lambda::bind` with `std::function` in Xcode13(clang version 13.0.0).  I have created a sample program that can shows the failure details.   
  
```  
#include <functional>  
#include <boost/lambda/bind.hpp>  
  
void func() {}  
  
int main() {  
    auto var = std::function<void()>(boost::lambda::bind(func));  
}  
```  
  
It seems to be that the template lambda_functor_base is undefined.  In digging through Boost, I cannot find a definition of the initial template.  There's a forward declaration, and explicit instantiations, but no default definition.  If I add a default definition (uncomment in the godbolt example below), it suddenly starts compiling. Any thoughts on it?  
  
https://godbolt.org/z/hx97Gova8  
  
Error details:  
  
```  
In file included from /opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/bind.hpp:15:  
In file included from /opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/core.hpp:57:  
/opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/detail/lambda_functors.hpp:140:31: error: implicit instantiation of undefined template 'boost::lambda::lambda_functor_base<boost::lambda::other_action<boost::lambda::assignment_action>, boost::tuples::tuple<boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>>, boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>>>>'  
class lambda_functor : public T   
                              ^  
/opt/compiler-explorer/clang-13.0.0/bin/../include/c++/v1/type_traits:2680:83: note: in instantiation of template class 'boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::other_action<boost::lambda::assignment_action>, boost::tuples::tuple<boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>>, boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>>>>>' requested here  
struct _LIBCPP_TEMPLATE_VIS is_assignable : _BoolConstant<__is_assignable(_Tp, _Up)> { };  
                                                                                  ^  
/opt/compiler-explorer/clang-13.0.0/bin/../include/c++/v1/type_traits:2722:14: note: in instantiation of template class 'std::is_assignable<const boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>> &, const boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>> &>' requested here  
    : public is_assignable<typename add_lvalue_reference<_Tp>::type,  
             ^  
/opt/compiler-explorer/clang-13.0.0/bin/../include/c++/v1/type_traits:505:28: note: in instantiation of template class 'std::is_copy_assignable<const boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>> &>' requested here  
__expand_to_true<_EnableIf<_Pred::value>...> __and_helper(int);  
                           ^  
/opt/compiler-explorer/clang-13.0.0/bin/../include/c++/v1/type_traits:509:44: note: while substituting explicitly-specified template arguments into function template '__and_helper'   
using _And _LIBCPP_NODEBUG_TYPE = decltype(__and_helper<_Pred...>(0));  
                                           ^  
/opt/compiler-explorer/clang-13.0.0/bin/../include/c++/v1/tuple:952:26: note: in instantiation of template type alias '_And' requested here  
    tuple& operator=(_If<_And<is_copy_assignable<_Tp>...>::value, tuple, __nat> const& __tuple)  
                         ^  
/opt/compiler-explorer/clang-13.0.0/bin/../include/c++/v1/tuple:1267:1: note: in instantiation of template class 'std::tuple<const boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>> &>' requested here  
forward_as_tuple(_Tp&&... __t) _NOEXCEPT  
^  
/opt/compiler-explorer/clang-13.0.0/bin/../include/c++/v1/__functional/function.h:155:44: note: in instantiation of function template specialization 'std::forward_as_tuple<const boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>> &>' requested here  
        : __f_(piecewise_construct, _VSTD::forward_as_tuple(__f),  
                                           ^  
/opt/compiler-explorer/clang-13.0.0/bin/../include/c++/v1/__functional/function.h:286:11: note: in instantiation of member function 'std::__function::__alloc_func<boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>>, std::allocator<boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>>>, void ()>::__alloc_func' requested here  
        : __f_(__f, _VSTD::move(__a)) {}  
          ^  
/opt/compiler-explorer/clang-13.0.0/bin/../include/c++/v1/__functional/function.h:312:33: note: in instantiation of member function 'std::__function::__func<boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>>, std::allocator<boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>>>, void ()>::__func' requested here  
    ::new ((void*)__hold.get()) __func(__f_.__target(), _Alloc(__a));  
                                ^  
/opt/compiler-explorer/clang-13.0.0/bin/../include/c++/v1/__functional/function.h:289:14: note: in instantiation of member function 'std::__function::__func<boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>>, std::allocator<boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>>>, void ()>::__clone' requested here  
    explicit __func(_Fp&& __f, _Alloc&& __a)  
             ^  
/opt/compiler-explorer/clang-13.0.0/bin/../include/c++/v1/__functional/function.h:405:44: note: in instantiation of member function 'std::__function::__func<boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>>, std::allocator<boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>>>, void ()>::__func' requested here  
                    ::new ((void*)&__buf_) _Fun(_VSTD::move(__f), _Alloc(__af));  
                                           ^  
/opt/compiler-explorer/clang-13.0.0/bin/../include/c++/v1/__functional/function.h:420:11: note: in instantiation of function template specialization 'std::__function::__value_func<void ()>::__value_func<boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>>, std::allocator<boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>>>>' requested here  
        : __value_func(_VSTD::forward<_Fp>(__f), allocator<_Fp>()) {}  
          ^  
/opt/compiler-explorer/clang-13.0.0/bin/../include/c++/v1/__functional/function.h:1118:50: note: in instantiation of function template specialization 'std::__function::__value_func<void ()>::__value_func<boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>>, void>' requested here  
function<_Rp(_ArgTypes...)>::function(_Fp __f) : __f_(_VSTD::move(__f)) {}  
                                                 ^  
<source>:17:12: note: in instantiation of function template specialization 'std::function<void ()>::function<boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>>, void>' requested here  
auto var = std::function<void()>(boost::lambda::bind(func));  
           ^  
/opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/detail/lambda_fwd.hpp:38:7: note: template is declared here  
class lambda_functor_base;  
      ^  
In file included from <source>:1:  
In file included from /opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/bind.hpp:15:  
In file included from /opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/core.hpp:57:  
/opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/detail/lambda_functors.hpp:162:5: error: implicit instantiation of undefined template 'boost::lambda::lambda_functor_base<boost::lambda::other_action<boost::lambda::assignment_action>, boost::tuples::tuple<boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>>, boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>>>>'  
    inherited::template sig<null_type>::type  
    ^  
/opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/detail/lambda_fwd.hpp:38:7: note: template is declared here  
class lambda_functor_base;  
      ^  
In file included from <source>:1:  
In file included from /opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/bind.hpp:15:  
In file included from /opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/core.hpp:57:  
/opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/detail/lambda_functors.hpp:191:12: error: implicit instantiation of undefined template 'boost::lambda::lambda_functor_base<boost::lambda::other_action<boost::lambda::assignment_action>, boost::tuples::tuple<boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>>, boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>>>>'  
  typename inherited::template sig<tuple<A&> >::type  
           ^  
/opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/detail/lambda_fwd.hpp:38:7: note: template is declared here  
class lambda_functor_base;  
      ^  
In file included from <source>:1:  
In file included from /opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/bind.hpp:15:  
In file included from /opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/core.hpp:57:  
/opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/detail/lambda_functors.hpp:199:37: error: implicit instantiation of undefined template 'boost::lambda::lambda_functor_base<boost::lambda::other_action<boost::lambda::assignment_action>, boost::tuples::tuple<boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>>, boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>>>>'  
  BOOST_LAMBDA_DISABLE_IF_ARRAY1(A, inherited::template sig<tuple<A const&> >)  
                                    ^  
/opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/detail/lambda_fwd.hpp:38:7: note: template is declared here  
class lambda_functor_base;  
      ^  
In file included from <source>:1:  
In file included from /opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/bind.hpp:15:  
In file included from /opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/core.hpp:57:  
/opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/detail/lambda_functors.hpp:207:12: error: implicit instantiation of undefined template 'boost::lambda::lambda_functor_base<boost::lambda::other_action<boost::lambda::assignment_action>, boost::tuples::tuple<boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>>, boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>>>>'  
  typename inherited::template sig<tuple<A&, B&> >::type  
           ^  
/opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/detail/lambda_fwd.hpp:38:7: note: template is declared here  
class lambda_functor_base;  
      ^  
In file included from <source>:1:  
In file included from /opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/bind.hpp:15:  
In file included from /opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/core.hpp:57:  
/opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/detail/lambda_functors.hpp:215:40: error: implicit instantiation of undefined template 'boost::lambda::lambda_functor_base<boost::lambda::other_action<boost::lambda::assignment_action>, boost::tuples::tuple<boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>>, boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>>>>'  
  BOOST_LAMBDA_DISABLE_IF_ARRAY2(A, B, inherited::template sig<tuple<A const&, B&> >)  
                                       ^  
/opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/detail/lambda_fwd.hpp:38:7: note: template is declared here  
class lambda_functor_base;  
      ^  
In file included from <source>:1:  
In file included from /opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/bind.hpp:15:  
In file included from /opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/core.hpp:57:  
/opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/detail/lambda_functors.hpp:223:40: error: implicit instantiation of undefined template 'boost::lambda::lambda_functor_base<boost::lambda::other_action<boost::lambda::assignment_action>, boost::tuples::tuple<boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>>, boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>>>>'  
  BOOST_LAMBDA_DISABLE_IF_ARRAY2(A, B, inherited::template sig<tuple<A&, B const&> >)  
                                       ^  
/opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/detail/lambda_fwd.hpp:38:7: note: template is declared here  
class lambda_functor_base;  
      ^  
In file included from <source>:1:  
In file included from /opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/bind.hpp:15:  
In file included from /opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/core.hpp:57:  
/opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/detail/lambda_functors.hpp:231:40: error: implicit instantiation of undefined template 'boost::lambda::lambda_functor_base<boost::lambda::other_action<boost::lambda::assignment_action>, boost::tuples::tuple<boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>>, boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>>>>'  
  BOOST_LAMBDA_DISABLE_IF_ARRAY2(A, B, inherited::template sig<tuple<A const&, B const&> >)  
                                       ^  
/opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/detail/lambda_fwd.hpp:38:7: note: template is declared here  
class lambda_functor_base;  
      ^  
In file included from <source>:1:  
In file included from /opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/bind.hpp:15:  
In file included from /opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/core.hpp:57:  
/opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/detail/lambda_functors.hpp:239:12: error: implicit instantiation of undefined template 'boost::lambda::lambda_functor_base<boost::lambda::other_action<boost::lambda::assignment_action>, boost::tuples::tuple<boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>>, boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>>>>'  
  typename inherited::template sig<tuple<A&, B&, C&> >::type  
           ^  
/opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/detail/lambda_fwd.hpp:38:7: note: template is declared here  
class lambda_functor_base;  
      ^  
In file included from <source>:1:  
In file included from /opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/bind.hpp:15:  
In file included from /opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/core.hpp:57:  
/opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/detail/lambda_functors.hpp:248:43: error: implicit instantiation of undefined template 'boost::lambda::lambda_functor_base<boost::lambda::other_action<boost::lambda::assignment_action>, boost::tuples::tuple<boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>>, boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>>>>'  
  BOOST_LAMBDA_DISABLE_IF_ARRAY3(A, B, C, inherited::template sig<tuple<A const&, B const&, C const&> >)  
                                          ^  
/opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/detail/lambda_fwd.hpp:38:7: note: template is declared here  
class lambda_functor_base;  
      ^  
In file included from <source>:1:  
In file included from /opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/bind.hpp:15:  
In file included from /opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/core.hpp:57:  
/opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/detail/lambda_functors.hpp:258:12: error: implicit instantiation of undefined template 'boost::lambda::lambda_functor_base<boost::lambda::other_action<boost::lambda::assignment_action>, boost::tuples::tuple<boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>>, boost::lambda::lambda_functor<boost::lambda::lambda_functor_base<boost::lambda::action<1, boost::lambda::function_action<1, void>>, boost::tuples::tuple<void (&)()>>>>>'  
  typename inherited::template sig<tuple<CALL_REFERENCE_TYPES> >::type  
           ^  
/opt/compiler-explorer/libs/boost_1_75_0/boost/lambda/detail/lambda_fwd.hpp:38:7: note: template is declared here  
class lambda_functor_base;  
      ^  
11 errors generated.  
```

---

## Comment 1

> Username: kkumar45  
> Created at: 2021-10-20 06:44:13 UTC  
> Url: https://github.com/boostorg/lambda/issues/24#issuecomment-947375247  

Can someone take a look at it?

---

## Comment 2

> Username: pdimov  
> Created at: 2021-12-26 17:37:47 UTC  
> Url: https://github.com/boostorg/lambda/issues/24#issuecomment-1001218986  

The reason this fails is because libc++ tries to instantiate `std::is_assignable`, which checks whether `operator=` exists, which for lambda function objects is overloaded, but its definition isn't included by `<boost/lambda/bind.hpp>`. This can be fixed if the appropriate headers are included in `bind.hpp`, as in https://godbolt.org/z/YsEnEK7qP.  
  
Interestingly, if I include `<boost/lambda/lambda.hpp>` instead, as in https://godbolt.org/z/cWh9vooaP, it fails because of a bug in libc++ - it uses `operator&` directly in its implementation, instead of `std::addressof`. Attn @mclow. :-)

---

## Comment 3

> Username: mclow  
> Created at: 2021-12-29 17:28:09 UTC  
> Url: https://github.com/boostorg/lambda/issues/24#issuecomment-1002700978  

See https://reviews.llvm.org/D116380 for libc++ change.
