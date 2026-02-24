# #143 - Add a real-world example of creating a new data type [Open]

> Username: ldionne  
> Created at: 2015-06-20 21:25:04 UTC  
> Updated at: 2015-07-15 18:53:47 UTC  
> Url: https://github.com/boostorg/hana/issues/143  

Use a real-world example to show how to extend the library with custom data types.

---

## Comment 1

> Username: cppljevans  
> Created at: 2015-07-15 17:48:27 UTC  
> Updated at: 2015-07-15 18:02:23 UTC  
> Url: https://github.com/boostorg/hana/issues/143#issuecomment-121691360  

+5.  I'm currently trying to do this with data_val defined here:  
  
https://gist.github.com/cppljevans/d5ebf19a14d79409e4e7  
  
However, after making several tries, I still get obscure compile error messages:  
  
In file included from data_val.cpp:4:  
In file included from ./data_val.hpp:11:  
In file included from /home/evansl/prog_dev/boost/sandbox/rw/sandbox/ldionne/hana/include/boost/hana/comparable.hpp:15:  
In file included from /home/evansl/prog_dev/boost/sandbox/rw/sandbox/ldionne/hana/include/boost/hana/bool.hpp:16:  
/home/evansl/prog_dev/boost/sandbox/rw/sandbox/ldionne/hana/include/boost/hana/constant.hpp:53:9: error:   
      static_assert failed "hana::value<T>() requires T to be a Constant"  
        static_assert(_models<Constant, C>{},  
        ^             ~~~~~~~~~~~~~~~~~~~~~~  
/home/evansl/prog_dev/boost/sandbox/rw/sandbox/ldionne/hana/include/boost/hana/tuple.hpp:547:29: note: in  
      instantiation of function template specialization 'boost::hana::value<bool>' requested here  
            hana::if_(hana::value<decltype(  
                            ^  
/home/evansl/prog_dev/boost/sandbox/rw/sandbox/ldionne/hana/include/boost/hana/tuple.hpp:564:45: note: during template  
      argument deduction for class template partial specialization 'find_tail_size<_tuple<type-parameter-0-0,  
      type-parameter-0-1...>, type-parameter-0-2,  
      whenhana::if_(hana::value<decltype(detail::std::declval<Pred()(detail::std::declval<X>()))>(), true, false)>  
      >' [with X = data_val<1>, Xs = <data_val<2>>, Pred = boost::hana::_partialboost::hana::_equal<,  
      boost::hana::detail::closure_impl<boost::hana::detail::element<0, data_val<1> > > >]  
            detail::std::size_t tail_size = find_tail_size<  
                                            ^  
/home/evansl/prog_dev/boost/sandbox/rw/sandbox/ldionne/hana/include/boost/hana/tuple.hpp:570:24: note: in  
      instantiation of default argument for 'find_if_implboost::hana::_tuple<data_val<1, data_val<2> >,  
      boost::hana::_partialboost::hana::_equal<, boost::hana::detail::closure_impl<boost::hana::detail::element<0,  
      data_val<1> > > > >' required here  
        constexpr auto find_if_impl(Xs&& xs, Pred const&, int) {  
                       ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/home/evansl/prog_dev/boost/sandbox/rw/sandbox/ldionne/hana/include/boost/hana/tuple.hpp:592:37: note: while  
      substituting deduced template arguments into function template 'find_if_impl' [with Xs =  
      boost::hana::_tuple<data_val<1>, data_val<2> >, Pred = boost::hana::_partialboost::hana::_equal<,  
      boost::hana::detail::closure_impl<boost::hana::detail::element<0, data_val<1> > > >, tail_size = (no value)]  
        BOOST_HANA_PP_FOR_EACH_REF1(BOOST_HANA_PP_FIND_IF)  
                                    ^  
/home/evansl/prog_dev/boost/sandbox/rw/sandbox/ldionne/hana/include/boost/hana/tuple.hpp:150:9: note: expanded from  
      macro 'BOOST_HANA_PP_FOR_EACH_REF1'  
        MACRO(&&)                                                       \  
        ^  
/home/evansl/prog_dev/boost/sandbox/rw/sandbox/ldionne/hana/include/boost/hana/tuple.hpp:587:24: note: expanded from  
      macro 'BOOST_HANA_PP_FIND_IF'  
                return tuple_detail::find_if_impl(                          \  
                       ^  
/home/evansl/prog_dev/boost/sandbox/rw/sandbox/ldionne/hana/include/boost/hana/fwd/searchable.hpp:524:28: note: in  
      instantiation of function template specialization 'boost::hana::find_if_impl<boost::hana::Tuple,  
      void>::apply<data_val<1>, data_val<2> , boost::hana::_partialboost::hana::_equal<,  
      boost::hana::detail::closure_impl<boost::hana::detail::element<0, data_val<1> > > > >' requested here  
            return FindIf::apply(static_cast<Xs&&>(xs), static_cast<Pred&&>(pred));  
                           ^  
/home/evansl/prog_dev/boost/sandbox/rw/sandbox/ldionne/hana/include/boost/hana/set.hpp:106:20: note: in instantiation  
      of function template specialization 'boost::hana::_find_if::operator()boost::hana::_tuple<data_val<1,  
      data_val<2> >, boost::hana::_partialboost::hana::_equal<,  
      boost::hana::detail::closure_impl<boost::hana::detail::element<0, data_val<1> > > > >' requested here  
            return hana::find_if(static_cast<Set&&>(set).storage,  
                   ^  
/home/evansl/prog_dev/boost/sandbox/rw/sandbox/ldionne/hana/include/boost/hana/fwd/searchable.hpp:524:28: note: in  
      instantiation of function template specialization 'boost::hana::find_if_impl<boost::hana::Set,  
      void>::applyboost::hana::_set<data_val<1, data_val<2> >, boost::hana::_partialboost::hana::_equal<,  
      boost::hana::detail::closure_impl<boost::hana::detail::element<0, data_val<1> > > > >' requested here  
            return FindIf::apply(static_cast<Xs&&>(xs), static_cast<Pred&&>(pred));  
                           ^  
/home/evansl/prog_dev/boost/sandbox/rw/sandbox/ldionne/hana/include/boost/hana/searchable.hpp:182:20: note: in  
      instantiation of function template specialization  
      'boost::hana::_find_if::operator()boost::hana::_set<data_val<1, data_val<2> >,  
      boost::hana::_partialboost::hana::_equal<, boost::hana::detail::closure_impl<boost::hana::detail::element<0,  
      data_val<1> > > > >' requested here  
            return hana::find_if(static_cast<Xs&&>(xs),  
                   ^  
/home/evansl/prog_dev/boost/sandbox/rw/sandbox/ldionne/hana/include/boost/hana/fwd/searchable.hpp:577:26: note: in  
      instantiation of function template specialization 'boost::hana::find_impl<boost::hana::Set,  
      boost::hana::when<true> >::applyboost::hana::_set<data_val<1, data_val<2> >, data_val<1> >' requested here  
            return Find::apply(static_cast<Xs&&>(xs), static_cast<Key&&>(key));  
                         ^  
data_val.cpp:18:8: note: in instantiation of function template specialization  
      'boost::hana::_find::operator()boost::hana::_set<data_val<1, data_val<2> >, data_val<1> >' requested here  
      =find(make_set(Val{}...),ValToFind{});  
       ^  
data_val.cpp:33:28: note: in instantiation of function template specialization 'find_val_equal<data_val<1>,  
      data_val<1>, data_val<2> >' requested here  
    auto val_found_maybe_v=find_val_equal<data_val<1>,data_val<1>,data_val<2>>();  
                           ^  
In file included from data_val.cpp:4:  
In file included from ./data_val.hpp:11:  
In file included from /home/evansl/prog_dev/boost/sandbox/rw/sandbox/ldionne/hana/include/boost/hana/comparable.hpp:15:  
In file included from /home/evansl/prog_dev/boost/sandbox/rw/sandbox/ldionne/hana/include/boost/hana/bool.hpp:16:  
/home/evansl/prog_dev/boost/sandbox/rw/sandbox/ldionne/hana/include/boost/hana/constant.hpp:57:16: error: no matching  
      function for call to 'apply'  
        return Value::template apply<RawT>();  
               ^~~~~~~~~~~~~~~~~~~~~~~~~~~  
/home/evansl/prog_dev/boost/sandbox/rw/sandbox/ldionne/hana/include/boost/hana/detail/dispatch_if.hpp:19:31: note:   
      candidate function not viable: requires 1 argument, but 0 were provided  
        static constexpr auto apply(T&& ...) = delete;  
                              ^  
2 errors generated.  
make: **\* [/tmp/build/clangxx3_5_rel/boost/sandbox/rw/sandbox/lje/hana/sandbox/data_val.o] Error 1  
  
Compilation exited abnormally with code 2 at Wed Jul 15 12:22:19  
  
I have been working at this for many hours and I keep trying various things  
which I guess will work; however, each time, there's something more I'm  
missing and the compile error messages are not much help and the docs  
aren't much help either.  
  
Could you just show me how I get the .cpp file to compile, please?  
  
-regards,  
Larry

---

## Comment 2

> Username: ldionne  
> Created at: 2015-07-15 18:37:04 UTC  
> Url: https://github.com/boostorg/hana/issues/143#issuecomment-121707436  

Does the following work?  
  
``` c++  
#ifndef DATA_VAL_HPP_INCLUDED  
#define DATA_VAL_HPP_INCLUDED  
//Purpose:  
//  Provide a *very* simple template class  
//  to represent a set of arbitrary data types  
//  to help in testing other template classes.  
//  
//  Also, provide output operators for this simple  
//  template class to help in testing.  
#include <iostream>  
#include <boost/hana/comparable.hpp>  
  
  
template <unsigned TagVal>  
struct data_val {  
    static const unsigned value = TagVal;  
    using type = data_val;  
  
    // Needed for Constant  
    using value_type = unsigned;  
  
    // This gives a tag (like a Fusion tag) to data_val. The tag is data_val itself.  
    struct hana { using datatype = data_val; };  
};  
  
namespace boost{ namespace hana{  
    template <unsigned TagVal>  
    struct value_impl<data_val<TagVal>> {  
        template <typename T>  
        static constexpr auto apply()  
        { return T::value; }  
    };  
  
    template <unsigned TagVal0, unsigned TagVal1>  
    struct equal_impl<data_val<TagVal0>, data_val<TagVal1>> {  
        static constexpr auto apply(data_val<TagVal0> const&, data_val<TagVal1> const&) {  
            return hana::bool_<TagVal0 == TagVal1>;  
        }  
    };  
}}  
  
  template<unsigned TagVal>  
  std::ostream& operator<<( std::ostream&sout, data_val<TagVal>const&) {  
    sout << "data_val<" << TagVal << ">";  
    return sout;  
  }  
#endif  
  
#include <boost/hana/set.hpp>  
using namespace boost::hana;  
  
template <typename ValToFind, typename ... Val>  
auto find_val_equal() {  
    constexpr auto val_found_maybe_v = find(make_set(Val{}...), ValToFind{});  
    return val_found_maybe_v;  
}  
  
  
int main() {  
    std::cout<< data_val<1>{} << "\n";  
    std::cout<< equal(data_val<1>{}, data_val<1>{}) << "\n";  
    std::cout<< equal(data_val<1>{}, data_val<2>{}) << "\n";  
  
    //Test 1st law for Constant found here:  
    //  http://ldionne.com/hana/structboost_1_1hana_1_1Constant.html  
    constexpr auto test_compile_time_ctor = value<data_val<1>>();  
    auto val_found_maybe_v = find_val_equal<data_val<1>, data_val<1>, data_val<2>>();  
}  
```  
  
The problem is that you were returning a boolean from `equal`, not an  
IntegralConstant. However, you can also remove the implementation of  
`equal` altogether and one will be provided automatically, since  
`data_val` is a Constant and the value_type is unsigned, which is itself  
Comparable.

---

## Comment 3

> Username: cppljevans  
> Created at: 2015-07-15 18:53:47 UTC  
> Url: https://github.com/boostorg/hana/issues/143#issuecomment-121711319  

On 07/15/2015 01:37 PM, Louis Dionne wrote:  
  
> ```  
> template <unsigned TagVal>  
> struct value_impl<data_val<TagVal>> {  
>     template <typename T>  
>     static constexpr auto apply()  
>     { return T::value; }  
> };  
>   
> template <unsigned TagVal0, unsigned TagVal1>  
> struct equal_impl<data_val<TagVal0>, data_val<TagVal1>> {  
>     static constexpr auto apply(data_val<TagVal0> const&, data_val<TagVal1> const&) {  
>         return hana::bool_<TagVal0 == TagVal1>;  
>     }  
> };  
> ```  
  
Yes.  Thanks.
