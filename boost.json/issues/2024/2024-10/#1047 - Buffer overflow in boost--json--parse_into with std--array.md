# #1047 - Buffer overflow in boost::json::parse_into with std::array [Closed]

> Username: tyler92  
> Created at: 2024-10-06 13:18:18 UTC  
> Updated at: 2024-10-07 19:41:20 UTC  
> Closed at: 2024-10-07 19:41:19 UTC  
> Url: https://github.com/boostorg/json/issues/1047  

### Version of Boost  
  
`devel` branch, commit `85f9e92c18283d42737ebd067df4f881824e4edb`  
  
### Steps necessary to reproduce the problem  
  
```cpp  
#include <boost/json/parse_into.hpp>  
#include <boost/describe/class.hpp>  
  
struct Test  
{  
  std::array<std::int64_t, 3> arr;  
};  
  
BOOST_DESCRIBE_STRUCT(Test, (), (arr))  
  
int main()  
{  
  Test object{};  
  std::error_code ec;  
  
  std::string data = R"({"arr":[977,775500052916,9216,77552916,9216]})";  
  boost::json::parse_into(object, data, ec);  
  
  return 0;  
}  
```  
  
### All relevant compiler information  
  
Address sanitizer report a buffer overflow here:  
  
```  
=================================================================  
==233641==ERROR: AddressSanitizer: stack-buffer-overflow on address 0x7f5a9fc00038 at pc 0x559dfb6751d6 bp 0x7ffd547ca3a0 sp 0x7ffd547ca398  
WRITE of size 8 at 0x7f5a9fc00038 thread T0  
    #0 0x559dfb6751d5 in boost::json::detail::converting_handler<boost::json::detail::sequence_conversion_tag, std::array<long, 3ul>, boost::json::detail::converting_handler<boost::json::detail::described_class_conversion_tag, Test, boost::json::detail::into_handler<Test>>>::signal_value() boost/json/detail/parse_into.hpp:567:21  
    #1 0x559dfb674f9f in boost::json::detail::converting_handler<boost::json::detail::integral_conversion_tag, long, boost::json::detail::converting_handler<boost::json::detail::sequence_conversion_tag, std::array<long, 3ul>, boost::json::detail::converting_handler<boost::json::detail::described_class_conversion_tag, Test, boost::json::detail::into_handler<Test>>>>::on_int64(boost::system::error_code&, long) boost/json/detail/parse_into.hpp:298:24  
    #2 0x559dfb674cb0 in boost::json::detail::composite_handler<boost::json::detail::converting_handler<boost::json::detail::sequence_conversion_tag, std::array<long, 3ul>, boost::json::detail::converting_handler<boost::json::detail::described_class_conversion_tag, Test, boost::json::detail::into_handler<Test>>>, long, boost::json::detail::converting_handler<boost::json::detail::described_class_conversion_tag, Test, boost::json::detail::into_handler<Test>>, (boost::json::error)30>::on_int64(boost::system::error_code&, long) boost/json/detail/parse_into.hpp:223:9  
    #3 0x559dfb674a69 in auto boost::json::detail::converting_handler<boost::json::detail::described_class_conversion_tag, Test, boost::json::detail::into_handler<Test>>::on_int64(boost::system::error_code&, long)::'lambda'(auto&)::operator()<boost::json::detail::converting_handler<boost::json::detail::sequence_conversion_tag, std::array<long, 3ul>, boost::json::detail::converting_handler<boost::json::detail::described_class_conversion_tag, Test, boost::json::detail::into_handler<Test>>>>(auto&) const boost/json/detail/parse_into.hpp:1168:9  
    #4 0x559dfb674962 in bool boost::json::detail::tuple_handler_op_invoker<boost::json::detail::handler_tuple<boost::json::detail::converting_handler<boost::json::detail::described_class_conversion_tag, Test, boost::json::detail::into_handler<Test>>, boost::describe::detail::list<std::array<long, 3ul>>, boost::mp11::integer_sequence<unsigned long, 0ul>>, boost::json::detail::converting_handler<boost::json::detail::described_class_conversion_tag, Test, boost::json::detail::into_handler<Test>>::on_int64(boost::system::error_code&, long)::'lambda'(auto&)>::operator()<std::integral_constant<unsigned long, 0ul>>(auto) const boost/json/detail/parse_into.hpp:785:16  
    #5 0x559dfb6748b2 in decltype(std::declval<boost::json::detail::tuple_handler_op_invoker<boost::json::detail::handler_tuple<boost::json::detail::converting_handler<boost::json::detail::described_class_conversion_tag, Test, boost::json::detail::into_handler<Test>>, boost::describe::detail::list<std::array<long, 3ul>>, boost::mp11::integer_sequence<unsigned long, 0ul>>, boost::json::detail::converting_handler<boost::json::detail::described_class_conversion_tag, Test, boost::json::detail::into_handler<Test>>::on_int64(boost::system::error_code&, long)::'lambda'(auto&)>>()(decltype(__declval<auto>(0)) std::declval<std::integral_constant<unsigned long, 0ul>>()())) boost::mp11::detail::mp_with_index_impl_<1ul>::call<0ul, boost::json::detail::tuple_handler_op_invoker<boost::json::detail::handler_tuple<boost::json::detail::converting_handler<boost::json::detail::described_class_conversion_tag, Test, boost::json::detail::into_handler<Test>>, boost::describe::detail::list<std::array<long, 3ul>>, boost::mp11::integer_sequence<unsigned long, 0ul>>, boost::json::detail::converting_handler<boost::json::detail::described_class_conversion_tag, Test, boost::json::detail::into_handler<Test>>::on_int64(boost::system::error_code&, long)::'lambda'(auto&)>>(unsigned long, boost::json::detail::tuple_handler_op_invoker<boost::json::detail::handler_tuple<boost::json::detail::converting_handler<boost::json::detail::described_class_conversion_tag, Test, boost::json::detail::into_handler<Test>>, boost::describe::detail::list<std::array<long, 3ul>>, boost::mp11::integer_sequence<unsigned long, 0ul>>, boost::json::detail::converting_handler<boost::json::detail::described_class_conversion_tag, Test, boost::json::detail::into_handler<Test>>::on_int64(boost::system::error_code&, long)::'lambda'(auto&)>&&) boost/mp11/detail/mp_with_index.hpp:62:16  
    #6 0x559dfb67482f in decltype(std::declval<boost::json::detail::tuple_handler_op_invoker<boost::json::detail::handler_tuple<boost::json::detail::converting_handler<boost::json::detail::described_class_conversion_tag, Test, boost::json::detail::into_handler<Test>>, boost::describe::detail::list<std::array<long, 3ul>>, boost::mp11::integer_sequence<unsigned long, 0ul>>, boost::json::detail::converting_handler<boost::json::detail::described_class_conversion_tag, Test, boost::json::detail::into_handler<Test>>::on_int64(boost::system::error_code&, long)::'lambda'(auto&)>>()(decltype(__declval<auto>(0)) std::declval<std::integral_constant<unsigned long, 0ul>>()())) boost::mp11::mp_with_index<1ul, boost::json::detail::tuple_handler_op_invoker<boost::json::detail::handler_tuple<boost::json::detail::converting_handler<boost::json::detail::described_class_conversion_tag, Test, boost::json::detail::into_handler<Test>>, boost::describe::detail::list<std::array<long, 3ul>>, boost::mp11::integer_sequence<unsigned long, 0ul>>, boost::json::detail::converting_handler<boost::json::detail::described_class_conversion_tag, Test, boost::json::detail::into_handler<Test>>::on_int64(boost::system::error_code&, long)::'lambda'(auto&)>>(unsigned long, boost::json::detail::tuple_handler_op_invoker<boost::json::detail::handler_tuple<boost::json::detail::converting_handler<boost::json::detail::described_class_conversion_tag, Test, boost::json::detail::into_handler<Test>>, boost::describe::detail::list<std::array<long, 3ul>>, boost::mp11::integer_sequence<unsigned long, 0ul>>, boost::json::detail::converting_handler<boost::json::detail::described_class_conversion_tag, Test, boost::json::detail::into_handler<Test>>::on_int64(boost::system::error_code&, long)::'lambda'(auto&)>&&) boost/mp11/detail/mp_with_index.hpp:371:12  
    #7 0x559dfb674736 in decltype(std::declval<boost::json::detail::tuple_handler_op_invoker<boost::json::detail::handler_tuple<boost::json::detail::converting_handler<boost::json::detail::described_class_conversion_tag, Test, boost::json::detail::into_handler<Test>>, boost::describe::detail::list<std::array<long, 3ul>>, boost::mp11::integer_sequence<unsigned long, 0ul>>, boost::json::detail::converting_handler<boost::json::detail::described_class_conversion_tag, Test, boost::json::detail::into_handler<Test>>::on_int64(boost::system::error_code&, long)::'lambda'(auto&)>>()(decltype(__declval<auto>(0)) std::declval<std::integral_constant<unsigned long, 0ul>>()())) boost::mp11::mp_with_index<std::integral_constant<unsigned long, 1ul>, boost::json::detail::tuple_handler_op_invoker<boost::json::detail::handler_tuple<boost::json::detail::converting_handler<boost::json::detail::described_class_conversion_tag, Test, boost::json::detail::into_handler<Test>>, boost::describe::detail::list<std::array<long, 3ul>>, boost::mp11::integer_sequence<unsigned long, 0ul>>, boost::json::detail::converting_handler<boost::json::detail::described_class_conversion_tag, Test, boost::json::detail::into_handler<Test>>::on_int64(boost::system::error_code&, long)::'lambda'(auto&)>>(unsigned long, boost::json::detail::tuple_handler_op_invoker<boost::json::detail::handler_tuple<boost::json::detail::converting_handler<boost::json::detail::described_class_conversion_tag, Test, boost::json::detail::into_handler<Test>>, boost::describe::detail::list<std::array<long, 3ul>>, boost::mp11::integer_sequence<unsigned long, 0ul>>, boost::json::detail::converting_handler<boost::json::detail::described_class_conversion_tag, Test, boost::json::detail::into_handler<Test>>::on_int64(boost::system::error_code&, long)::'lambda'(auto&)>&&) boost/mp11/detail/mp_with_index.hpp:376:12  
    #8 0x559dfb6745e2 in boost::json::detail::converting_handler<boost::json::detail::described_class_conversion_tag, Test, boost::json::detail::into_handler<Test>>::on_int64(boost::system::error_code&, long) boost/json/detail/parse_into.hpp:1168:9  
    #9 0x559dfb672dcb in boost::json::detail::into_handler<Test>::on_int64(long, boost::core::basic_string_view<char>, boost::system::error_code&) boost/json/detail/parse_into.hpp:1805:9  
    #10 0x559dfb699e4b in char const* boost::json::basic_parser<boost::json::detail::into_handler<Test>>::parse_number<true, (char)43, (boost::json::number_precision)0>(char const*, std::integral_constant<bool, true>, std::integral_constant<char, (char)43>, std::integral_constant<boost::json::number_precision, (boost::json::number_precision)0>) boost/json/basic_parser_impl.hpp:2712:12  
    #11 0x559dfb6951ec in char const* boost::json::basic_parser<boost::json::detail::into_handler<Test>>::parse_number_helper<true, (char)43>::operator()<0ul>(std::integral_constant<unsigned long, 0ul>) const boost/json/basic_parser_impl.hpp:206:24  
    #12 0x559dfb6950bc in decltype(std::declval<boost::json::basic_parser<boost::json::detail::into_handler<Test>>::parse_number_helper<true, (char)43>>()(decltype(__declval<std::integral_constant<unsigned long, 0ul>>(0)) std::declval<std::integral_constant<unsigned long, 0ul>>()())) boost::mp11::detail::mp_with_index_impl_<3ul>::call<0ul, boost::json::basic_parser<boost::json::detail::into_handler<Test>>::parse_number_helper<true, (char)43>>(unsigned long, boost::json::basic_parser<boost::json::detail::into_handler<Test>>::parse_number_helper<true, (char)43>&&) boost/mp11/detail/mp_with_index.hpp:86:24  
    #13 0x559dfb663daf in decltype(std::declval<boost::json::basic_parser<boost::json::detail::into_handler<Test>>::parse_number_helper<true, (char)43>>()(decltype(__declval<std::integral_constant<unsigned long, 0ul>>(0)) std::declval<std::integral_constant<unsigned long, 0ul>>()())) boost::mp11::mp_with_index<3ul, boost::json::basic_parser<boost::json::detail::into_handler<Test>>::parse_number_helper<true, (char)43>>(unsigned long, boost::json::basic_parser<boost::json::detail::into_handler<Test>>::parse_number_helper<true, (char)43>&&) boost/mp11/detail/mp_with_index.hpp:371:12  
    #14 0x559dfb66100b in char const* boost::json::basic_parser<boost::json::detail::into_handler<Test>>::parse_value<true, false>(char const*, std::integral_constant<bool, true>, std::integral_constant<bool, false>, bool, bool, bool) boost/json/basic_parser_impl.hpp:645:20  
    #15 0x559dfb66b2b3 in char const* boost::json::basic_parser<boost::json::detail::into_handler<Test>>::parse_array<true, false>(char const*, std::integral_constant<bool, true>, std::integral_constant<bool, false>, bool, bool, bool) boost/json/basic_parser_impl.hpp:1934:14  
    #16 0x559dfb66134b in char const* boost::json::basic_parser<boost::json::detail::into_handler<Test>>::parse_value<true, false>(char const*, std::integral_constant<bool, true>, std::integral_constant<bool, false>, bool, bool, bool) boost/json/basic_parser_impl.hpp:673:20  
    #17 0x559dfb66c575 in char const* boost::json::basic_parser<boost::json::detail::into_handler<Test>>::parse_object<true, false>(char const*, std::integral_constant<bool, true>, std::integral_constant<bool, false>, bool, bool, bool) boost/json/basic_parser_impl.hpp:1802:14  
    #18 0x559dfb6613ae in char const* boost::json::basic_parser<boost::json::detail::into_handler<Test>>::parse_value<true, false>(char const*, std::integral_constant<bool, true>, std::integral_constant<bool, false>, bool, bool, bool) boost/json/basic_parser_impl.hpp:675:20  
    #19 0x559dfb65d25a in char const* boost::json::basic_parser<boost::json::detail::into_handler<Test>>::parse_document<true>(char const*, std::integral_constant<bool, true>) boost/json/basic_parser_impl.hpp:562:14  
    #20 0x559dfb65b104 in boost::json::basic_parser<boost::json::detail::into_handler<Test>>::write_some(bool, char const*, unsigned long, boost::system::error_code&) boost/json/basic_parser_impl.hpp:2845:13  
    #21 0x559dfb65a2a4 in void boost::json::parse_into<Test>(Test&, boost::core::basic_string_view<char>, boost::system::error_code&, boost::json::parse_options const&) boost/json/impl/parse_into.hpp:31:23  
    #22 0x559dfb657869 in void boost::json::parse_into<Test>(Test&, boost::core::basic_string_view<char>, std::error_code&, boost::json::parse_options const&) boost/json/impl/parse_into.hpp:48:5  
    #23 0x559dfb65724a in main tets.cpp:17:5  
    #24 0x7f5aa16ebd8f in __libc_start_call_main csu/../sysdeps/nptl/libc_start_call_main.h:58:16  
    #25 0x7f5aa16ebe3f in __libc_start_main csu/../csu/libc-start.c:392:3  
    #26 0x559dfb57aa34 in _start (test-parse+0x8ca34) (BuildId: fd7dc28acd1857cab9b747a05d2edeed17ee77ab)  
  
Address 0x7f5a9fc00038 is located in stack of thread T0 at offset 56 in frame  
    #0 0x559dfb656d2f in main tets.cpp:12  
  
  This frame has 6 object(s):  
    [32, 56) 'object' (line 13) <== Memory access at offset 56 overflows this variable  
    [96, 112) 'ec' (line 14)  
    [128, 160) 'data' (line 16)  
    [192, 193) 'ref.tmp' (line 16)  
    [208, 224) 'agg.tmp'  
    [240, 256) 'ref.tmp1' (line 17)  
HINT: this may be a false positive if your program uses some custom stack unwind mechanism, swapcontext or vfork  
      (longjmp and C++ exceptions *are* supported)  
SUMMARY: AddressSanitizer: stack-buffer-overflow boost/json/detail/parse_into.hpp:567:21 in boost::json::detail::converting_handler<boost::json::detail::sequence_conversion_tag, std::array<long, 3ul>, boost::json::detail::converting_handler<boost::json::detail::described_class_conversion_tag, Test, boost::json::detail::into_handler<Test>>>::signal_value()  
Shadow bytes around the buggy address:  
  0x7f5a9fbffd80: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x7f5a9fbffe00: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x7f5a9fbffe80: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x7f5a9fbfff00: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x7f5a9fbfff80: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
=>0x7f5a9fc00000: f1 f1 f1 f1 00 00 00[f2]f2 f2 f2 f2 00 00 f2 f2  
  0x7f5a9fc00080: 00 00 00 00 f2 f2 f2 f2 f8 f2 00 00 f2 f2 00 00  
  0x7f5a9fc00100: f3 f3 f3 f3 00 00 00 00 00 00 00 00 00 00 00 00  
  0x7f5a9fc00180: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x7f5a9fc00200: f1 f1 f1 f1 00 f2 f2 f2 00 00 f2 f2 01 f2 01 f2  
  0x7f5a9fc00280: f8 f2 f8 f2 f8 f2 f8 f2 f8 f2 f8 f2 f8 f2 f8 f2  
Shadow byte legend (one shadow byte represents 8 application bytes):  
  Addressable:           00  
  Partially addressable: 01 02 03 04 05 06 07   
  Heap left redzone:       fa  
  Freed heap region:       fd  
  Stack left redzone:      f1  
  Stack mid redzone:       f2  
  Stack right redzone:     f3  
  Stack after return:      f5  
  Stack use after scope:   f8  
  Global redzone:          f9  
  Global init order:       f6  
  Poisoned by user:        f7  
  Container overflow:      fc  
  Array cookie:            ac  
  Intra object redzone:    bb  
  ASan internal:           fe  
  Left alloca redzone:     ca  
  Right alloca redzone:    cb  
==233641==ABORTING  
```

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-10-07 07:53:37 UTC  
> Url: https://github.com/boostorg/json/issues/1047#issuecomment-2396182037  

Thank you for catching this!
