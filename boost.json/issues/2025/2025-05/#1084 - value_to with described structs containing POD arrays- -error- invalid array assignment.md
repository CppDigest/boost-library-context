# #1084 - value_to with described structs containing POD arrays: "error: invalid array assignment" [Open]

> Username: hsteinhaus  
> Created at: 2025-05-23 15:41:28 UTC  
> Updated at: 2025-05-26 12:30:49 UTC  
> Url: https://github.com/boostorg/json/issues/1084  

When trying to serialize a struct containing a array of int, I get the following error:  
```  
/opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/boost/json/detail/value_to.hpp:387:33: error: invalid array assignment  
  387 |             (*res).* D::pointer = std::move(*member_res);  
      |             ~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/c++/13.3.0/bits/ranges_base.h:37,  
                 from /opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/c++/13.3.0/bits/ranges_util.h:34,  
                 from /opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/c++/13.3.0/tuple:44,  
                 from /opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/boost/mp11/tuple.hpp:15,  
                 from /opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/boost/mp11.hpp:20,  
                 from /home/holger-local/CLionProjects/untitled/main.cpp:4:  
/opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/c++/13.3.0/bits/stl_iterator.h: In instantiation of ‘class std::insert_iterator<int [2]>’:  
/opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/boost/json/detail/value_to.hpp:271:32:   required from ‘boost::json::result<T> boost::json::detail::value_to_impl(sequence_conversion_tag, boost::json::try_value_to_tag<T>, const boost::json::value&, const Ctx&) [with T = int [2]; Ctx = std::tuple<allow_exceptions, no_context>; boost::json::result<T> = boost::system::result<int [2], boost::system::error_code>]’  
/opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/boost/json/value_to.hpp:230:33:   required from ‘typename boost::json::result_for<T, boost::json::value>::type boost::json::try_value_to(const value&, const Context&) [with T = int [2]; Context = std::tuple<detail::allow_exceptions, detail::no_context>; typename result_for<T, value>::type = boost::system::result<int [2], boost::system::error_code>]’  
/opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/boost/json/detail/value_to.hpp:381:42:   required from ‘void boost::json::detail::to_described_member<Ctx, T, non_throwing>::operator()(I) [with I = std::integral_constant<long unsigned int, 2>; Ctx = std::tuple<boost::json::detail::allow_exceptions, boost::json::detail::no_context>; T = app::A; bool non_throwing = true]’  
/opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/boost/mp11/algorithm.hpp:1060:29:   required from ‘constexpr F boost::mp11::detail::mp_for_each_impl(boost::mp11::mp_list<Q ...>, F&&) [with T = {std::integral_constant<long unsigned int, 0>, std::integral_constant<long unsigned int, 1>, std::integral_constant<long unsigned int, 2>}; F = boost::json::detail::to_described_member<std::tuple<boost::json::detail::allow_exceptions, boost::json::detail::no_context>, app::A, true>&]’  
/opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/boost/mp11/algorithm.hpp:1093:36:   required from ‘boost::json::result<T> boost::json::detail::value_to_impl(described_class_conversion_tag, boost::json::try_value_to_tag<T>, const boost::json::value&, const Ctx&) [with T = app::A; Ctx = std::tuple<allow_exceptions, no_context>; boost::json::result<T> = boost::system::result<app::A, boost::system::error_code>]’  
/opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/boost/json/detail/value_to.hpp:855:25:   required from ‘T boost::json::detail::value_to_impl(Impl, boost::json::value_to_tag<T>, const boost::json::value&, const Ctx&) [with Impl = described_class_conversion_tag; T = app::A; Ctx = no_context]’  
/opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/boost/json/value_to.hpp:96:33:   required from ‘T boost::json::value_to(const value&, const Context&) [with T = app::A; Context = detail::no_context]’  
/opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/boost/json/value_to.hpp:148:23:   required from ‘T boost::json::value_to(const value&) [with T = app::A]’  
/home/holger-local/CLionProjects/untitled/main.cpp:42:43:   required from here  
/opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/c++/13.3.0/bits/stl_iterator.h:966:7: error: ‘int [2]’ is not a class, struct, or union type  
  966 |       operator=(const typename _Container::value_type& __value)  
      |       ^~~~~~~~  
/opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/c++/13.3.0/bits/stl_iterator.h:975:7: error: ‘int [2]’ is not a class, struct, or union type  
  975 |       operator=(typename _Container::value_type&& __value)  
      |       ^~~~~~~~  
/opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/boost/json/detail/value_to.hpp: In instantiation of ‘boost::json::result<T> boost::json::detail::value_to_impl(sequence_conversion_tag, boost::json::try_value_to_tag<T>, const boost::json::value&, const Ctx&) [with T = int [2]; Ctx = std::tuple<allow_exceptions, no_context>; boost::json::result<T> = boost::system::result<int [2], boost::system::error_code>]’:  
/opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/boost/json/value_to.hpp:230:33:   required from ‘typename boost::json::result_for<T, boost::json::value>::type boost::json::try_value_to(const value&, const Context&) [with T = int [2]; Context = std::tuple<detail::allow_exceptions, detail::no_context>; typename result_for<T, value>::type = boost::system::result<int [2], boost::system::error_code>]’  
/opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/boost/json/detail/value_to.hpp:381:42:   required from ‘void boost::json::detail::to_described_member<Ctx, T, non_throwing>::operator()(I) [with I = std::integral_constant<long unsigned int, 2>; Ctx = std::tuple<boost::json::detail::allow_exceptions, boost::json::detail::no_context>; T = app::A; bool non_throwing = true]’  
/opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/boost/mp11/algorithm.hpp:1060:29:   required from ‘constexpr F boost::mp11::detail::mp_for_each_impl(boost::mp11::mp_list<Q ...>, F&&) [with T = {std::integral_constant<long unsigned int, 0>, std::integral_constant<long unsigned int, 1>, std::integral_constant<long unsigned int, 2>}; F = boost::json::detail::to_described_member<std::tuple<boost::json::detail::allow_exceptions, boost::json::detail::no_context>, app::A, true>&]’  
/opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/boost/mp11/algorithm.hpp:1093:36:   required from ‘boost::json::result<T> boost::json::detail::value_to_impl(described_class_conversion_tag, boost::json::try_value_to_tag<T>, const boost::json::value&, const Ctx&) [with T = app::A; Ctx = std::tuple<allow_exceptions, no_context>; boost::json::result<T> = boost::system::result<app::A, boost::system::error_code>]’  
/opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/boost/json/detail/value_to.hpp:855:25:   required from ‘T boost::json::detail::value_to_impl(Impl, boost::json::value_to_tag<T>, const boost::json::value&, const Ctx&) [with Impl = described_class_conversion_tag; T = app::A; Ctx = no_context]’  
/opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/boost/json/value_to.hpp:96:33:   required from ‘T boost::json::value_to(const value&, const Context&) [with T = app::A; Context = detail::no_context]’  
/opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/boost/json/value_to.hpp:148:23:   required from ‘T boost::json::value_to(const value&) [with T = app::A]’  
/home/holger-local/CLionProjects/untitled/main.cpp:42:43:   required from here  
/opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/boost/json/detail/value_to.hpp:277:16: error: no match for ‘operator=’ (operand types are ‘std::insert_iterator<int [2]>’ and ‘std::remove_reference<int&>::type’ {aka ‘int’})  
  277 |         *ins++ = std::move(*elem_res);  
      |         ~~~~~~~^~~~~~~~~~~~~~~~~~~~~~  
/opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/c++/13.3.0/bits/stl_iterator.h:904:11: note: candidate: ‘constexpr std::insert_iterator<int [2]>& std::insert_iterator<int [2]>::operator=(const std::insert_iterator<int [2]>&)’  
  904 |     class insert_iterator  
      |           ^~~~~~~~~~~~~~~  
/opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/c++/13.3.0/bits/stl_iterator.h:904:11: note:   no known conversion for argument 1 from ‘std::remove_reference<int&>::type’ {aka ‘int’} to ‘const std::insert_iterator<int [2]>&’  
/opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/c++/13.3.0/bits/stl_iterator.h:904:11: note: candidate: ‘constexpr std::insert_iterator<int [2]>& std::insert_iterator<int [2]>::operator=(std::insert_iterator<int [2]>&&)’  
/opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/c++/13.3.0/bits/stl_iterator.h:904:11: note:   no known conversion for argument 1 from ‘std::remove_reference<int&>::type’ {aka ‘int’} to ‘std::insert_iterator<int [2]>&&’  
/opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/boost/json/detail/value_to.hpp:279:12: error: could not convert ‘result’ from ‘int [2]’ to ‘boost::json::result<int [2]>’ {aka ‘boost::system::result<int [2], boost::system::error_code>’}  
  279 |     return result;  
      |            ^~~~~~  
      |            |  
      |            int [2]  
In file included from /opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/boost/json/conversion.hpp:398,  
                 from /opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/boost/json.hpp:17:  
/opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/boost/json/impl/conversion.hpp: In instantiation of ‘std::insert_iterator<_Container> boost::json::detail::inserter(T&, boost::mp11::mp_int<0>) [with T = int [2]; boost::mp11::mp_int<0> = std::integral_constant<int, 0>]’:  
/opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/boost/json/detail/value_to.hpp:271:32:   required from ‘boost::json::result<T> boost::json::detail::value_to_impl(sequence_conversion_tag, boost::json::try_value_to_tag<T>, const boost::json::value&, const Ctx&) [with T = int [2]; Ctx = std::tuple<allow_exceptions, no_context>; boost::json::result<T> = boost::system::result<int [2], boost::system::error_code>]’  
/opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/boost/json/value_to.hpp:230:33:   required from ‘typename boost::json::result_for<T, boost::json::value>::type boost::json::try_value_to(const value&, const Context&) [with T = int [2]; Context = std::tuple<detail::allow_exceptions, detail::no_context>; typename result_for<T, value>::type = boost::system::result<int [2], boost::system::error_code>]’  
/opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/boost/json/detail/value_to.hpp:381:42:   required from ‘void boost::json::detail::to_described_member<Ctx, T, non_throwing>::operator()(I) [with I = std::integral_constant<long unsigned int, 2>; Ctx = std::tuple<boost::json::detail::allow_exceptions, boost::json::detail::no_context>; T = app::A; bool non_throwing = true]’  
/opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/boost/mp11/algorithm.hpp:1060:29:   required from ‘constexpr F boost::mp11::detail::mp_for_each_impl(boost::mp11::mp_list<Q ...>, F&&) [with T = {std::integral_constant<long unsigned int, 0>, std::integral_constant<long unsigned int, 1>, std::integral_constant<long unsigned int, 2>}; F = boost::json::detail::to_described_member<std::tuple<boost::json::detail::allow_exceptions, boost::json::detail::no_context>, app::A, true>&]’  
/opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/boost/mp11/algorithm.hpp:1093:36:   required from ‘boost::json::result<T> boost::json::detail::value_to_impl(described_class_conversion_tag, boost::json::try_value_to_tag<T>, const boost::json::value&, const Ctx&) [with T = app::A; Ctx = std::tuple<allow_exceptions, no_context>; boost::json::result<T> = boost::system::result<app::A, boost::system::error_code>]’  
/opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/boost/json/detail/value_to.hpp:855:25:   required from ‘T boost::json::detail::value_to_impl(Impl, boost::json::value_to_tag<T>, const boost::json::value&, const Ctx&) [with Impl = described_class_conversion_tag; T = app::A; Ctx = no_context]’  
/opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/boost/json/value_to.hpp:96:33:   required from ‘T boost::json::value_to(const value&, const Context&) [with T = app::A; Context = detail::no_context]’  
/opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/boost/json/value_to.hpp:148:23:   required from ‘T boost::json::value_to(const value&) [with T = app::A]’  
/home/holger-local/CLionProjects/untitled/main.cpp:42:43:   required from here  
/opt/fslc-emo/6.6-scarthgap/sysroots/cortexa53-fslc-linux/usr/include/boost/json/impl/conversion.hpp:170:42: error: request for member ‘end’ in ‘target’, which is of non-class type ‘int [2]’  
  170 |     return std::inserter( target, target.end() );  
      |                                   ~~~~~~~^~~  
```  
  
To reproduce the error, I have modified the example boost::describe is providing here: https://www.boost.org/doc/libs/latest/libs/describe/doc/html/describe.html#example_from_json . Unfortunetely, there seems to be no easy work-around with a custom tag_invoke, because an implentation for the array is already available by default.  
  
```  
#include <boost/describe.hpp>  
#include <boost/mp11.hpp>  
#include <boost/json.hpp>  
#include <boost/version.hpp>  
#include <type_traits>  
  
using namespace boost::json;  
  
namespace app  
{  
  
  
    struct A  
    {  
        int x;  
        int y;  
        int b[2];  // the breaking change  
    };  
  
BOOST_DESCRIBE_STRUCT(A, (), (x, y, b))  
  
} // namespace app  
  
#include <iostream>  
  
int main()  
{  
    boost::json::value jv{ { "x", 1 }, { "y", 2 }, {"b", {1, 2}} };  
  
    std::cout << "jv: " << jv << std::endl;  
  
    auto a = boost::json::value_to<app::A>( jv );  
  
    std::cout << "a: { " << a.x << ", " << a.y << " }" << std::endl;  
}  
  
```  
  
The compiler used for the error output given above is a custom embedded cross compiler (GCC 13.3.0), but I do not expect that this problem is specific to this compiler.

---

## Comment 1

> Username: grisumbras  
> Created at: 2025-05-26 09:08:27 UTC  
> Url: https://github.com/boostorg/json/issues/1084#issuecomment-2909023033  

We currently don't support arrays for this direction of conversion. We only support them for the opposite direction (serialization). Currently, you can convert the array to `std::array`. I'll keep the issue open in order to see if I can add array support for `value_to` and `parse_into`.

---

## Comment 2

> Username: hsteinhaus  
> Created at: 2025-05-26 11:40:35 UTC  
> Url: https://github.com/boostorg/json/issues/1084#issuecomment-2909429908  

@grisumbras Thanks for the clarification. Unfortunately I cannot change the struct definitions, as they are not owned by my project (originating from an embedded device, plain C firmware).

---

## Comment 3

> Username: grisumbras  
> Created at: 2025-05-26 12:30:48 UTC  
> Url: https://github.com/boostorg/json/issues/1084#issuecomment-2909582299  

`vaue_to` returns its result. And functions can't return arrays.
