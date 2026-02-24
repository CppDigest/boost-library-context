# #436 - V2: Storing expression by value [Closed]

> Username: apolukhin  
> Created at: 2019-01-09 20:18:51 UTC  
> Updated at: 2019-04-25 13:21:09 UTC  
> Closed at: 2019-04-25 13:19:36 UTC  
> Url: https://github.com/boostorg/spirit/issues/436  

The following example causes ASAN failures:  
```  
// g++-8 -c -m64 -std=c++14 -O0 -fsanitize=address -Wall -o spirit.o spirit.cpp && g++-8 -m64 -fsanitize=address -Wl,-O1 -o 08_spirit_rules spirit.o && ./08_spirit_rules  
  
#include <boost/spirit/include/qi.hpp>  
#include <string>  
#include <stdexcept>  
#include <cassert>  
  
void set_hours(unsigned short val) { assert(val == 10); }  
  
void parse_datetime_cxx11_bug(const std::string& s = "10:") {  
    // Use unsigned short as output type; require Radix 10 and from 2 to 2 digits.  
    const boost::spirit::qi::uint_parser<unsigned short, 10, 2, 2> u2_;  
    const auto time_parser = (u2_ [ &set_hours ] >> ':');  
  
    const char* first = s.data();  
    const char* const end = first + s.size();  
    const bool success = boost::spirit::qi::parse(first, end, time_parser);  
  
    if (!success || first != end) {  
        throw std::logic_error("Parsing of '" + s + "' failed");  
    }  
} // end of parse_datetime_cxx11() function  
  
  
int main() {  
  parse_datetime_cxx11_bug();  
}  
```  
  
ASAN reports stack use after scope:  
```  
==6391==ERROR: AddressSanitizer: stack-use-after-scope on address 0x7ffdb75987c0 at pc 0x000000407494 bp 0x7ffdb7597d20 sp 0x7ffdb7597d10  
READ of size 1 at 0x7ffdb75987c0 thread T0  
    #0 0x407493 in boost::fusion::cons<char, boost::fusion::nil_>::cons(char const&) (/home/antoshkka/workspace/08_spirit_rules+0x407493)  
    #1 0x40750b in boost::fusion::cons<boost::spirit::detail::as_meta_element<char>::type, boost::fusion::nil_> boost::spirit::detail::make_cons<char>(char const&) (/home/antoshkka/workspace/08_spirit_rules+0x40750b)  
    #2 0x406ff8 in boost::spirit::detail::make_terminal_impl<boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char const&>, 0l> const&, boost::fusion::nil_ const&, boost::spirit::unused_type&, boost::spirit::qi::domain>::operator()(boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char const&>, 0l> const&, boost::fusion::nil_ const&, boost::spirit::unused_type&) const (/home/antoshkka/workspace/08_spirit_rules+0x406ff8)  
    #3 0x406055 in boost::spirit::detail::make_binary_helper<boost::spirit::meta_compiler<boost::spirit::qi::domain>::meta_grammar>::impl<boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char const&>, 0l> const&, boost::fusion::nil_, boost::spirit::unused_type&>::operator()(boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char const&>, 0l> const&, boost::fusion::nil_ const&, boost::spirit::unused_type&) const (/home/antoshkka/workspace/08_spirit_rules+0x406055)  
    #4 0x40579f in boost::proto::if_<boost::proto::detail::has_tag<boost::proto::tagns_::tag::shift_right>, boost::proto::reverse_fold<boost::proto::_, boost::proto::_state, boost::proto::detail::reverse_fold_tree_<boost::proto::tagns_::tag::shift_right, boost::spirit::detail::make_binary_helper<boost::spirit::meta_compiler<boost::spirit::qi::domain>::meta_grammar> > >, boost::spirit::detail::make_binary_helper<boost::spirit::meta_compiler<boost::spirit::qi::domain>::meta_grammar> >::impl<boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char const&>, 0l> const&, boost::fusion::nil_, boost::spirit::unused_type&>::operator()(boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char const&>, 0l> const&, boost::fusion::nil_ const&, boost::spirit::unused_type&) const (/home/antoshkka/workspace/08_spirit_rules+0x40579f)  
    #5 0x404dc0 in boost::proto::detail::reverse_fold_impl<boost::proto::make<boost::fusion::nil_>, boost::proto::detail::reverse_fold_tree_<boost::proto::tagns_::tag::shift_right, boost::spirit::detail::make_binary_helper<boost::spirit::meta_compiler<boost::spirit::qi::domain>::meta_grammar> >, boost::proto::exprns_::expr<boost::proto::tagns_::tag::shift_right, boost::proto::argsns_::list2<boost::proto::exprns_::expr<boost::proto::tagns_::tag::subscript, boost::proto::argsns_::list2<boost::spirit::terminal<boost::spirit::tag::uint_parser<unsigned short, 10u, 2u, 2> > const&, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<void (* const&)(unsigned short)>, 0l> >, 2l> const&, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char const&>, 0l> >, 2l> const&, mpl_::void_ const&, boost::spirit::unused_type&, 2l>::operator()(boost::proto::exprns_::expr<boost::proto::tagns_::tag::shift_right, boost::proto::argsns_::list2<boost::proto::exprns_::expr<boost::proto::tagns_::tag::subscript, boost::proto::argsns_::list2<boost::spirit::terminal<boost::spirit::tag::uint_parser<unsigned short, 10u, 2u, 2> > const&, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<void (* const&)(unsigned short)>, 0l> >, 2l> const&, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char const&>, 0l> >, 2l> const&, mpl_::void_ const&, boost::spirit::unused_type&) const (/home/antoshkka/workspace/08_spirit_rules+0x404dc0)  
    #6 0x4044e8 in boost::spirit::detail::make_binary<boost::spirit::qi::domain, boost::proto::tagns_::tag::shift_right, boost::spirit::meta_compiler<boost::spirit::qi::domain>::meta_grammar, true>::impl<boost::proto::exprns_::expr<boost::proto::tagns_::tag::shift_right, boost::proto::argsns_::list2<boost::proto::exprns_::expr<boost::proto::tagns_::tag::subscript, boost::proto::argsns_::list2<boost::spirit::terminal<boost::spirit::tag::uint_parser<unsigned short, 10u, 2u, 2> > const&, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<void (* const&)(unsigned short)>, 0l> >, 2l> const&, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char const&>, 0l> >, 2l> const&, mpl_::void_ const&, boost::spirit::unused_type&>::operator()(boost::proto::exprns_::expr<boost::proto::tagns_::tag::shift_right, boost::proto::argsns_::list2<boost::proto::exprns_::expr<boost::proto::tagns_::tag::subscript, boost::proto::argsns_::list2<boost::spirit::terminal<boost::spirit::tag::uint_parser<unsigned short, 10u, 2u, 2> > const&, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<void (* const&)(unsigned short)>, 0l> >, 2l> const&, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char const&>, 0l> >, 2l> const&, mpl_::void_ const&, boost::spirit::unused_type&) const (/home/antoshkka/workspace/08_spirit_rules+0x4044e8)  
    #7 0x403711 in boost::spirit::result_of::compile<boost::spirit::qi::domain, boost::proto::exprns_::expr<boost::proto::tagns_::tag::shift_right, boost::proto::argsns_::list2<boost::proto::exprns_::expr<boost::proto::tagns_::tag::subscript, boost::proto::argsns_::list2<boost::spirit::terminal<boost::spirit::tag::uint_parser<unsigned short, 10u, 2u, 2> > const&, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<void (* const&)(unsigned short)>, 0l> >, 2l> const&, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char const&>, 0l> >, 2l>, boost::spirit::unused_type, void>::type boost::spirit::detail::compiler<boost::spirit::qi::domain>::compile<boost::proto::exprns_::expr<boost::proto::tagns_::tag::shift_right, boost::proto::argsns_::list2<boost::proto::exprns_::expr<boost::proto::tagns_::tag::subscript, boost::proto::argsns_::list2<boost::spirit::terminal<boost::spirit::tag::uint_parser<unsigned short, 10u, 2u, 2> > const&, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<void (* const&)(unsigned short)>, 0l> >, 2l> const&, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char const&>, 0l> >, 2l>, boost::spirit::unused_type>(boost::proto::exprns_::expr<boost::proto::tagns_::tag::shift_right, boost::proto::argsns_::list2<boost::proto::exprns_::expr<boost::proto::tagns_::tag::subscript, boost::proto::argsns_::list2<boost::spirit::terminal<boost::spirit::tag::uint_parser<unsigned short, 10u, 2u, 2> > const&, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<void (* const&)(unsigned short)>, 0l> >, 2l> const&, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char const&>, 0l> >, 2l> const&, boost::spirit::unused_type, mpl_::bool_<true>) (/home/antoshkka/workspace/08_spirit_rules+0x403711)  
    #8 0x403439 in boost::spirit::result_of::compile<boost::spirit::qi::domain, boost::proto::exprns_::expr<boost::proto::tagns_::tag::shift_right, boost::proto::argsns_::list2<boost::proto::exprns_::expr<boost::proto::tagns_::tag::subscript, boost::proto::argsns_::list2<boost::spirit::terminal<boost::spirit::tag::uint_parser<unsigned short, 10u, 2u, 2> > const&, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<void (* const&)(unsigned short)>, 0l> >, 2l> const&, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char const&>, 0l> >, 2l>, boost::spirit::unused_type, void>::type boost::spirit::compile<boost::spirit::qi::domain, boost::proto::exprns_::expr<boost::proto::tagns_::tag::shift_right, boost::proto::argsns_::list2<boost::proto::exprns_::expr<boost::proto::tagns_::tag::subscript, boost::proto::argsns_::list2<boost::spirit::terminal<boost::spirit::tag::uint_parser<unsigned short, 10u, 2u, 2> > const&, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<void (* const&)(unsigned short)>, 0l> >, 2l> const&, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char const&>, 0l> >, 2l> >(boost::proto::exprns_::expr<boost::proto::tagns_::tag::shift_right, boost::proto::argsns_::list2<boost::proto::exprns_::expr<boost::proto::tagns_::tag::subscript, boost::proto::argsns_::list2<boost::spirit::terminal<boost::spirit::tag::uint_parser<unsigned short, 10u, 2u, 2> > const&, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<void (* const&)(unsigned short)>, 0l> >, 2l> const&, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char const&>, 0l> >, 2l> const&) (/home/antoshkka/workspace/08_spirit_rules+0x403439)  
    #9 0x403190 in bool boost::spirit::qi::detail::parse_impl<boost::proto::exprns_::expr<boost::proto::tagns_::tag::shift_right, boost::proto::argsns_::list2<boost::proto::exprns_::expr<boost::proto::tagns_::tag::subscript, boost::proto::argsns_::list2<boost::spirit::terminal<boost::spirit::tag::uint_parser<unsigned short, 10u, 2u, 2> > const&, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<void (* const&)(unsigned short)>, 0l> >, 2l> const&, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char const&>, 0l> >, 2l>, void>::call<char const*>(char const*&, char const*, boost::proto::exprns_::expr<boost::proto::tagns_::tag::shift_right, boost::proto::argsns_::list2<boost::proto::exprns_::expr<boost::proto::tagns_::tag::subscript, boost::proto::argsns_::list2<boost::spirit::terminal<boost::spirit::tag::uint_parser<unsigned short, 10u, 2u, 2> > const&, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<void (* const&)(unsigned short)>, 0l> >, 2l> const&, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char const&>, 0l> >, 2l> const&) (/home/antoshkka/workspace/08_spirit_rules+0x403190)  
    #10 0x402f35 in bool boost::spirit::qi::parse<char const*, boost::proto::exprns_::expr<boost::proto::tagns_::tag::shift_right, boost::proto::argsns_::list2<boost::proto::exprns_::expr<boost::proto::tagns_::tag::subscript, boost::proto::argsns_::list2<boost::spirit::terminal<boost::spirit::tag::uint_parser<unsigned short, 10u, 2u, 2> > const&, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<void (* const&)(unsigned short)>, 0l> >, 2l> const&, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char const&>, 0l> >, 2l> >(char const*&, char const*, boost::proto::exprns_::expr<boost::proto::tagns_::tag::shift_right, boost::proto::argsns_::list2<boost::proto::exprns_::expr<boost::proto::tagns_::tag::subscript, boost::proto::argsns_::list2<boost::spirit::terminal<boost::spirit::tag::uint_parser<unsigned short, 10u, 2u, 2> > const&, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<void (* const&)(unsigned short)>, 0l> >, 2l> const&, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char const&>, 0l> >, 2l> const&) (/home/antoshkka/workspace/08_spirit_rules+0x402f35)  
    #11 0x4021cf in parse_datetime_cxx11_bug(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) (/home/antoshkka/workspace/08_spirit_rules+0x4021cf)  
    #12 0x40258e in main (/home/antoshkka/workspace/08_spirit_rules+0x40258e)  
    #13 0x7f57c3f3682f in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x2082f)  
    #14 0x401918 in _start (/home/antoshkka/workspace/08_spirit_rules+0x401918)  
  
Address 0x7ffdb75987c0 is located in stack of thread T0 at offset 96 in frame  
    #0 0x401a07 in parse_datetime_cxx11_bug(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) (/home/antoshkka/workspace/08_spirit_rules+0x401a07)  
  
  This frame has 18 object(s):  
    [32, 33) 'u2_'  
    [96, 97) '<unknown>' <== Memory access at offset 96 is inside this variable  
    [160, 161) '<unknown>'  
    [224, 225) '<unknown>'  
    [288, 289) '<unknown>'  
    [352, 353) '<unknown>'  
    [416, 417) '<unknown>'  
    [480, 481) '<unknown>'  
    [544, 552) '<unknown>'  
    [608, 616) 'first'  
    [672, 680) 'that'  
    [736, 744) 'that'  
    [800, 816) 'time_parser'  
    [864, 880) '<unknown>'  
    [928, 944) 'that'  
    [992, 1008) 'that'  
    [1056, 1088) '<unknown>'  
    [1120, 1152) '<unknown>'  
HINT: this may be a false positive if your program uses some custom stack unwind mechanism or swapcontext  
      (longjmp and C++ exceptions *are* supported)  
SUMMARY: AddressSanitizer: stack-use-after-scope (/home/antoshkka/workspace/08_spirit_rules+0x407493) in boost::fusion::cons<char, boost::fusion::nil_>::cons(char const&)  
Shadow bytes around the buggy address:  
  0x100036eab0a0: 00 00 00 00 f1 f1 f1 f1 01 f2 f2 f2 00 00 00 00  
  0x100036eab0b0: 00 00 00 00 00 00 00 00 00 00 f1 f1 f1 f1 00 f2  
  0x100036eab0c0: f2 f2 f2 f2 f2 f2 00 00 00 f2 00 00 00 00 00 00  
  0x100036eab0d0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x100036eab0e0: 00 00 00 00 00 00 00 00 00 00 00 00 f1 f1 f1 f1  
=>0x100036eab0f0: 01 f2 f2 f2 f2 f2 f2 f2[f8]f2 f2 f2 f2 f2 f2 f2  
  0x100036eab100: f8 f2 f2 f2 f2 f2 f2 f2 f8 f2 f2 f2 f2 f2 f2 f2  
  0x100036eab110: f8 f2 f2 f2 f2 f2 f2 f2 f8 f2 f2 f2 f2 f2 f2 f2  
  0x100036eab120: f8 f2 f2 f2 f2 f2 f2 f2 f8 f2 f2 f2 f2 f2 f2 f2  
  0x100036eab130: f8 f2 f2 f2 f2 f2 f2 f2 00 f2 f2 f2 f2 f2 f2 f2  
  0x100036eab140: f8 f2 f2 f2 f2 f2 f2 f2 f8 f2 f2 f2 f2 f2 f2 f2  
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
==6391==ABORTING  
```

---

## Comment 1

> Username: Kojoley  
> Created at: 2019-01-09 21:34:36 UTC  
> Updated at: 2019-01-14 19:38:40 UTC  
> Url: https://github.com/boostorg/spirit/issues/436#issuecomment-452876442  

Proto creates references to parsers, even in constructors, that's why you have references to temporaries. You can make copy of the parser with `qi::copy` (that's what `BOOST_SPIRIT_AUTO` does). It should be possible to fix Proto to make copies of prvalues.

---

## Comment 2

> Username: apolukhin  
> Created at: 2019-01-10 08:24:21 UTC  
> Url: https://github.com/boostorg/spirit/issues/436#issuecomment-453009634  

So the best thing to do is to move that issue to the Boost.Proto issues?

---

## Comment 3

> Username: Kojoley  
> Created at: 2019-01-10 11:48:58 UTC  
> Url: https://github.com/boostorg/spirit/issues/436#issuecomment-453068848  

You can try. While it is by design (Proto has `by_value_generator`, but Spirit uses the default one) at least a static assert can be added to prevent that kind of bugs.

---

## Comment 4

> Username: djowel  
> Created at: 2019-04-25 13:19:36 UTC  
> Url: https://github.com/boostorg/spirit/issues/436#issuecomment-486669349  

This is a proto issue. As mentioned, Proto creates references. There is a way to do a deep copy if needed: https://github.com/boostorg/spirit/blob/develop/include/boost/spirit/home/qi/copy.hpp  
  
I will close this issue now.

---

## Comment 5

> Username: djowel  
> Created at: 2019-04-25 13:21:09 UTC  
> Url: https://github.com/boostorg/spirit/issues/436#issuecomment-486669899  

Oh and BTW, X3 always does a deep copy of expression trees.
