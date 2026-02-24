# #391 - crash when expecting a std::string literal [Closed]

> Username: dvirtz  
> Created at: 2018-07-19 08:06:13 UTC  
> Updated at: 2019-03-06 21:12:04 UTC  
> Closed at: 2019-03-06 21:00:47 UTC  
> Url: https://github.com/boostorg/spirit/issues/391  

https://wandbox.org/permlink/qSp4Kkb1bLd5rl0u:  
```c++  
#include <boost/spirit/home/x3.hpp>  
namespace x3 = boost::spirit::x3;  
  
auto parser() {  
    std::string b = "5";  
    return x3::eps > b;  
}  
  
int main() {  
    std::string input = "5";  
    assert(x3::parse(input.begin(), input.end(), parser()));  
}  
```  
  
```  
terminating with uncaught exception of type boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::spirit::x3::expectation_failure<std::__1::__wrap_iter<char*> > > >: boost::spirit::x3::expectation_failure  
```  
  
It seems the cause is at https://github.com/boostorg/spirit/blob/b023ffa272b115815b6e714f8cc43bb9e09a9e14/include/boost/spirit/home/x3/string/literal_string.hpp#L231 where it should be `std::basic_string<Char>` instead of `Char const*`, as currently the parser stores a pointer to the string buffer which goes out of scope.  
  
The same code passes without problems with qi: https://wandbox.org/permlink/6xLC19SSZXQXjCp5

---

## Comment 1

> Username: Kojoley  
> Created at: 2019-03-06 21:00:47 UTC  
> Updated at: 2019-03-06 21:12:04 UTC  
> Url: https://github.com/boostorg/spirit/issues/391#issuecomment-470274621  

> it should be `std::basic_string<Char>` instead of `Char const*`  
  
No, it must not. At least not in your example (see explanation in the end).  
  
> currently the parser stores a pointer to the string buffer  
  
That's right.  
  
> which goes out of scope.  
  
Not the pointer, but the string itself.  
  
Recap: You are binding to a temporary string that will be out of scope (already destroyed) by the time when you are invoking the parser. With Qi you have the same bug in your program, but do not observe the problem just by luck. It is your fault, and unfortunately compilers do not do deep object lifetime analysis (it can change in future, see Herb Sutter talk), but you can use [AddressSanitize](https://github.com/google/sanitizers/wiki/AddressSanitizerUseAfterScope) to catch those bugs at run-time.  
  
Rationale: If you are passing a string object to Spirit it has to be generated at run-time and most of the users obviously do not want to have unneeded copy operation (that's why I am sure that #175 was wrong).  
  
Probably, we should move-in and store user string if it was passed by rvalue, but you did not pass your string as an rvalue.  
  
---  
  
<details><summary>Your Qi example under ASan:</summary>  
  
```  
==3424==ERROR: AddressSanitizer: stack-use-after-scope on address 0x7ffe99f71880 at pc 0x0000004fada8 bp 0x7ffe99f71290 sp 0x7ffe99f71288  
READ of size 8 at 0x7ffe99f71880 thread T0  
    #0 0x4fada7 in boost::spirit::detail::make_terminal_impl<boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&>, 0l> const&, boost::fusion::nil_ const&, boost::spirit::unused_type&, boost::spirit::qi::domain>::operator()(boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&>, 0l> const&, boost::fusion::nil_ const&, boost::spirit::unused_type&) const (/home/nick/spirit-test/qi+0x4fada7)  
    #1 0x4fa417 in boost::spirit::detail::make_binary_helper<boost::spirit::meta_compiler<boost::spirit::qi::domain>::meta_grammar>::impl<boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&>, 0l> const&, boost::fusion::nil_, boost::spirit::unused_type&>::operator()(boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&>, 0l> const&, boost::fusion::nil_ const&, boost::spirit::unused_type&) const (/home/nick/spirit-test/qi+0x4fa417)  
    #2 0x4f9f2f in boost::proto::detail::reverse_fold_impl<boost::proto::make<boost::fusion::nil_>, boost::proto::detail::reverse_fold_tree_<boost::proto::tagns_::tag::greater, boost::spirit::detail::make_binary_helper<boost::spirit::meta_compiler<boost::spirit::qi::domain>::meta_grammar> >, boost::proto::exprns_::expr<boost::proto::tagns_::tag::greater, boost::proto::argsns_::list2<boost::spirit::terminal<boost::spirit::tag::eps> const&, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&>, 0l> >, 2l> const&, mpl_::void_ const&, boost::spirit::unused_type&, 2l>::operator()(boost::proto::exprns_::expr<boost::proto::tagns_::tag::greater, boost::proto::argsns_::list2<boost::spirit::terminal<boost::spirit::tag::eps> const&, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&>, 0l> >, 2l> const&, mpl_::void_ const&, boost::spirit::unused_type&) const (/home/nick/spirit-test/qi+0x4f9f2f)  
    #3 0x4f9a49 in boost::spirit::detail::make_binary<boost::spirit::qi::domain, boost::proto::tagns_::tag::greater, boost::spirit::meta_compiler<boost::spirit::qi::domain>::meta_grammar, true>::impl<boost::proto::exprns_::expr<boost::proto::tagns_::tag::greater, boost::proto::argsns_::list2<boost::spirit::terminal<boost::spirit::tag::eps> const&, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&>, 0l> >, 2l> const&, mpl_::void_ const&, boost::spirit::unused_type&>::operator()(boost::proto::exprns_::expr<boost::proto::tagns_::tag::greater, boost::proto::argsns_::list2<boost::spirit::terminal<boost::spirit::tag::eps> const&, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&>, 0l> >, 2l> const&, mpl_::void_ const&, boost::spirit::unused_type&) const (/home/nick/spirit-test/qi+0x4f9a49)  
    #4 0x4f95ac in main (/home/nick/spirit-test/qi+0x4f95ac)  
    #5 0x7f8bfdf122e0 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x202e0)  
    #6 0x41f2d9 in _start (/home/nick/spirit-test/qi+0x41f2d9)  
  
Address 0x7ffe99f71880 is located in stack of thread T0 at offset 32 in frame  
    #0 0x4f937f in main (/home/nick/spirit-test/qi+0x4f937f)  
  
  This frame has 10 object(s):  
    [32, 64) 'b.i' <== Memory access at offset 32 is inside this variable  
    [96, 136) 'agg.tmp2.i.i.i.i.i.i'  
    [176, 184) 'iter.i.i.i.i'  
    [208, 209) 'ref.tmp.i.i.i.i.i'  
    [224, 225) 'modifiers.i.i.i.i'  
    [240, 241) 'ref.tmp.i.i.i.i'  
    [256, 264) 'last.i.i'  
    [288, 336) 'ref.tmp.i.i'  
    [368, 400) 'input'  
    [432, 448) 'ref.tmp4'  
HINT: this may be a false positive if your program uses some custom stack unwind mechanism, swapcontext or vfork  
      (longjmp and C++ exceptions *are* supported)  
SUMMARY: AddressSanitizer: stack-use-after-scope (/home/nick/spirit-test/qi+0x4fada7) in boost::spirit::detail::make_terminal_impl<boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&>, 0l> const&, boost::fusion::nil_ const&, boost::spirit::unused_type&, boost::spirit::qi::domain>::operator()(boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&>, 0l> const&, boost::fusion::nil_ const&, boost::spirit::unused_type&) const  
Shadow bytes around the buggy address:  
  0x1000533e62c0: 00 f3 f3 f3 f3 f3 f3 f3 00 00 00 00 00 00 00 00  
  0x1000533e62d0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x1000533e62e0: f1 f1 f1 f1 f8 f2 f2 f2 00 00 00 00 00 00 f2 f2  
  0x1000533e62f0: f2 f2 01 f3 00 00 00 00 00 00 00 00 00 00 00 00  
  0x1000533e6300: 00 00 00 00 00 00 00 00 00 00 00 00 f1 f1 f1 f1  
=>0x1000533e6310:[f8]f8 f8 f8 f2 f2 f2 f2 f8 f8 f8 f8 f8 f2 f2 f2  
  0x1000533e6320: f2 f2 f8 f2 f2 f2 01 f2 01 f2 01 f2 00 f2 f2 f2  
  0x1000533e6330: 00 00 00 00 00 00 f2 f2 f2 f2 00 00 00 00 f2 f2  
  0x1000533e6340: f2 f2 00 00 f3 f3 f3 f3 00 00 00 00 00 00 00 00  
  0x1000533e6350: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x1000533e6360: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
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
  Shadow gap:              cc  
==3424==ABORTING  
```  
  
</details>
