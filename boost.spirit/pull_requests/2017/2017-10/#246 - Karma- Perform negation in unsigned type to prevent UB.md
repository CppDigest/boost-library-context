# #246 Karma: Perform negation in unsigned type to prevent UB [Merged]

> Username: ecatmur  
> Created at: 2017-10-03 16:22:05 UTC  
> Updated at: 2018-02-24 15:00:37 UTC  
> Merged at: 2018-02-24 14:57:27 UTC  
> Closed at: 2018-02-24 14:57:27 UTC  
> Url: https://github.com/boostorg/spirit/pull/246  

When compiling with -fsanitize=undefined, `absolute_value` fails on minimal signed integer values:  
  
```  
../../../boost/spirit/home/karma/numeric/detail/numeric_utils.hpp:97:5: runtime error: negation of -9223372036854775808 cannot be represented in type 'long long int'; cast to an unsigned type to negate this value to itself  
    #0 0x404e9d in boost::spirit::traits::absolute_value<long long, void>::call(long long) ../../../boost/spirit/home/karma/numeric/detail/numeric_utils.hpp:97  
    #1 0x4343cd in boost::spirit::traits::absolute_value<long long, void>::type boost::spirit::traits::get_absolute_value<long long>(long long) ../../../boost/spirit/home/karma/numeric/detail/numeric_utils.hpp:149  
    #2 0x42892b in bool boost::spirit::karma::any_int_generator<long long, boost::spirit::unused_type, boost::spirit::unused_type, 10u, false>::insert_int<boost::spirit::karma::detail::output_iterator<std::back_insert_iterator<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, mpl_::int_<0>, boost::spirit::unused_type>, long long>(boost::spirit::karma::detail::output_iterator<std::back_insert_iterator<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, mpl_::int_<0>, boost::spirit::unused_type>&, long long const&) ../../../boost/spirit/home/karma/numeric/int.hpp:212  
    #3 0x41ee0c in bool boost::spirit::karma::any_int_generator<long long, boost::spirit::unused_type, boost::spirit::unused_type, 10u, false>::generate<boost::spirit::karma::detail::output_iterator<std::back_insert_iterator<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, mpl_::int_<0>, boost::spirit::unused_type>, boost::spirit::context<boost::fusion::cons<long long const&, boost::fusion::nil_>, boost::spirit::locals<mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na> >, boost::spirit::unused_type, long long>(boost::spirit::karma::detail::output_iterator<std::back_insert_iterator<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, mpl_::int_<0>, boost::spirit::unused_type>&, boost::spirit::context<boost::fusion::cons<long long const&, boost::fusion::nil_>, boost::spirit::locals<mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na> >&, boost::spirit::unused_type const&, long long const&) ../../../boost/spirit/home/karma/numeric/int.hpp:241  
    #4 0x416b1e in bool boost::spirit::karma::generate<std::back_insert_iterator<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, mpl_::int_<0>, boost::spirit::karma::int_generator<long long, 10u, false>, long long>(boost::spirit::karma::detail::output_iterator<std::back_insert_iterator<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, mpl_::int_<0>, boost::spirit::unused_type>&, boost::spirit::karma::int_generator<long long, 10u, false> const&, long long const&) ../../../boost/spirit/home/karma/generate.hpp:69  
    #5 0x40ec41 in bool boost::spirit::karma::generate<std::back_insert_iterator<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, boost::spirit::karma::int_generator<long long, 10u, false>, long long>(std::back_insert_iterator<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >&, boost::spirit::karma::int_generator<long long, 10u, false> const&, long long const&) ../../../boost/spirit/home/karma/generate.hpp:91  
    #6 0x409b8d in bool spirit_test::test<char, boost::spirit::karma::int_generator<long long, 10u, false>, long long>(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, boost::spirit::karma::int_generator<long long, 10u, false> const&, long long const&) karma/test.hpp:148  
    #7 0x407513 in void test_minmax::operator()<long long>(long long) const karma/int3.cpp:58  
    #8 0x4070ac in void boost::mpl::aux::for_each_impl<false>::execute<boost::mpl::v_iter<boost::mpl::vector<long long, unsigned long long, short, unsigned short, int, unsigned int, long, unsigned long, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, 0l>, boost::mpl::v_iter<boost::mpl::vector<long long, unsigned long long, short, unsigned short, int, unsigned int, long, unsigned long, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, 8l>, boost::mpl::identity<mpl_::na>, test_minmax>(boost::mpl::v_iter<boost::mpl::vector<long long, unsigned long long, short, unsigned short, int, unsigned int, long, unsigned long, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, 0l>*, boost::mpl::v_iter<boost::mpl::vector<long long, unsigned long long, short, unsigned short, int, unsigned int, long, unsigned long, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, 8l>*, boost::mpl::identity<mpl_::na>*, test_minmax) ../../../boost/mpl/for_each.hpp:78  
    #9 0x406b77 in void boost::mpl::for_each<boost::mpl::vector<long long, unsigned long long, short, unsigned short, int, unsigned int, long, unsigned long, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, boost::mpl::identity<mpl_::na>, test_minmax>(test_minmax, boost::mpl::vector<long long, unsigned long long, short, unsigned short, int, unsigned int, long, unsigned long, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>*, boost::mpl::identity<mpl_::na>*) ../../../boost/mpl/for_each.hpp:104  
    #10 0x406582 in void boost::mpl::for_each<boost::mpl::vector<long long, unsigned long long, short, unsigned short, int, unsigned int, long, unsigned long, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, test_minmax>(test_minmax, boost::mpl::vector<long long, unsigned long long, short, unsigned short, int, unsigned int, long, unsigned long, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>*) ../../../boost/mpl/for_each.hpp:118  
    #11 0x402bfc in main karma/int3.cpp:125  
    #12 0x7f327a527d5c in __libc_start_main (/lib64/libc.so.6+0x1ed5c)  
    #13 0x402af8  (boost/develop/bin.v2/libs/spirit/test/karma_int3-p3.test/gcc-gnu-5.4.0/debug/karma_int3-p3+0x402af8)  
```  
  
The simplest UB-safe way to take the absolute value of a signed integral type into the corresponding unsigned type is to cast to unsigned *first*, negate in the unsigned type, and if necessary cast to unsigned again to account for integral promotion.  
  
Code generation is not always the same but is not materially worse; at -O3/-Ox (and without ubsan) the main compilers generate the following for long long:  
  
          |   before      |   after  
    gcc   | sar, xor, sub | cqo, xor, sub  
    clang | neg, cmov     | neg, cmov  
    icc   | sar, xor, sub | neg, test, cmovge  
    MSVC  | cdq, xor, sub | cdq, xor, sub

---

## Comment 1

> Username: djowel  
> Created_at: 2017-10-03 22:30:34 UTC  
> Url: https://github.com/boostorg/spirit/pull/246#issuecomment-333997970  

@hkaiser , tell me what you think? is this good to go?

---

## Comment 2

> Username: Kojoley  
> Created_at: 2018-02-24 01:47:56 UTC  
> Updated_at: 2018-02-24 01:55:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/246#issuecomment-368187112  

Argh, MSVC is paranoid.  
  
Suppressing the warning inside the macro is a complicated thing.  
For `(~n + 1u)` MSVC generates `test, jns, not, inc` what is not good again.  
The PR #369 generates this:  
  
Compiler   | ASM  
---------- | ----------------------------  
GCC 4.8.1+ | cqo, xor, sub  
Clang 3.4+ | mov, neg, cmovl  
ICC 18     | test, jns, neg  
VS2017     | test, jns, mov, cmp, je, neg  
  
I am so mad of MSVC...

---

## Comment 3

> Username: Kojoley  
> Created_at: 2018-02-24 14:57:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/246#issuecomment-368233896  

Thanks @ecatmur, sorry for the delay!

---

## Comment 4

> Username: hkaiser  
> Created_at: 2018-02-24 15:00:37 UTC  
> Url: https://github.com/boostorg/spirit/pull/246#issuecomment-368234068  

Good catch, thanks!

---
