# #410 Karma: Fixed wrong buffer char type on Windows [Merged]

> Username: Kojoley  
> Created at: 2018-10-24 13:50:44 UTC  
> Updated at: 2018-10-25 10:55:04 UTC  
> Merged at: 2018-10-25 10:55:01 UTC  
> Closed at: 2018-10-25 10:55:01 UTC  
> Url: https://github.com/boostorg/spirit/pull/410  

With `BOOST_SPIRIT_UNICODE` defined Clang, MinGW and Cygwin were using `wchar_t`.  
  
I have switched to OS detection with `_WIN32`, it will give the right results for most compilers except Cygwin.  
  
Actually buffer char type should not be chosen that way, but it looks like nobody cares.  
  
<details><summary>Error log for search engines</summary>  
  
Clang  
-----  
```  
In file included from regression_unicode_char.cpp:14:  
In file included from ./boost/spirit/home/karma/nonterminal/grammar.hpp:18:  
In file included from ./boost/spirit/home/karma/nonterminal/rule.hpp:38:  
./boost/spirit/home/karma/detail/output_iterator.hpp:225:13: error: static_assert failed "sizeof(T) <= sizeof(buffer_char_type)"  
            BOOST_STATIC_ASSERT(sizeof(T) <= sizeof(buffer_char_type));  
            ^                   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
./boost/static_assert.hpp:70:41: note: expanded from macro 'BOOST_STATIC_ASSERT'  
#     define BOOST_STATIC_ASSERT( ... ) static_assert(__VA_ARGS__, #__VA_ARGS__)  
                                        ^             ~~~~~~~~~~~  
./boost/spirit/home/karma/detail/output_iterator.hpp:292:25: note: in instantiation of function template specialization 'boost::spirit::karma::detail::buffer_sink::output<unsigned int>' requested here  
                buffer->output(value);  
                        ^  
./boost/spirit/home/karma/detail/output_iterator.hpp:370:44: note: in instantiation of function template specialization 'boost::spirit::karma::detail::buffering_policy::output<unsigned int>' requested here  
            return this->buffering_policy::output(value);  
                                           ^  
./boost/spirit/home/karma/detail/output_iterator.hpp:470:38: note: in instantiation of function template specialization 'boost::spirit::karma::detail::disabling_output_iterator<boost::spirit::karma::detail::buffering_policy, boost::spirit::karma::detail::counting_policy<boost::spirit::karma::detail::output_iterator<std::back_insert_iterator<std::basic_string<unsigned int, std::char_traits<unsigned int>, std::allocator<unsigned int> > >, mpl_::int_<15>, boost::spirit::unused_type> >, boost::spirit::karma::detail::position_policy>::output<unsigned int>' requested here  
            if (this->base_iterator::output(value))  
                                     ^  
./boost/spirit/home/karma/detail/generate_to.hpp:30:15: note: in instantiation of function template specialization 'boost::spirit::karma::detail::output_iterator<std::back_insert_iterator<std::basic_string<unsigned int, std::char_traits<unsigned int>, std::allocator<unsigned int> > >, mpl_::int_<15>, boost::spirit::unused_type>::operator=<unsigned int>' requested here  
        *sink = spirit::char_class::convert<CharEncoding>::to(Tag(), p);  
              ^  
./boost/spirit/home/karma/char/char_generator.hpp:81:35: note: in instantiation of function template specialization 'boost::spirit::karma::detail::generate_to<boost::spirit::karma::detail::output_iterator<std::back_insert_iterator<std::basic_string<unsigned int, std::char_traits<unsigned int>, std::allocator<unsigned int> > >, mpl_::int_<15>, boost::spirit::unused_type>, unsigned int, boost::spirit::char_encoding::unicode, boost::spirit::unused_type>' requested here  
            return karma::detail::generate_to(sink, ch, char_encoding(), tag()) &&  
                                  ^  
./boost/spirit/home/karma/nonterminal/detail/generator_binder.hpp:42:22: note: (skipping 6 contexts in backtrace; use -ftemplate-backtrace-limit=0 to see all)  
            return g.generate(sink, context, delim  
                     ^  
./boost/function/function_template.hpp:1126:5: note: in instantiation of function template specialization 'boost::function<bool (boost::spirit::karma::detail::output_iterator<std::back_insert_iterator<std::basic_string<unsigned int, std::char_traits<unsigned int>, std::allocator<unsigned int> > >, mpl_::int_<15>, boost::spirit::unused_type> &, boost::spirit::context<boost::fusion::cons<const unsigned int &, boost::fusion::nil_>, boost::fusion::vector<> > &, const boost::spirit::unused_type &)>::function<boost::spirit::karma::detail::generator_binder<boost::spirit::karma::any_char<boost::spirit::char_encoding::unicode, boost::spirit::unused_type>, mpl_::bool_<false> > >' requested here  
    self_type(f).swap(*this);  
    ^  
./boost/spirit/home/karma/nonterminal/rule.hpp:192:19: note: in instantiation of function template specialization 'boost::function<bool (boost::spirit::karma::detail::output_iterator<std::back_insert_iterator<std::basic_string<unsigned int, std::char_traits<unsigned int>, std::allocator<unsigned int> > >, mpl_::int_<15>, boost::spirit::unused_type> &, boost::spirit::context<boost::fusion::cons<const unsigned int &, boost::fusion::nil_>, boost::fusion::vector<> > &, const boost::spirit::unused_type &)>::operator=<boost::spirit::karma::detail::generator_binder<boost::spirit::karma::any_char<boost::spirit::char_encoding::unicode, boost::spirit::unused_type>, mpl_::bool_<false> > >' requested here  
            lhs.f = detail::bind_generator<Auto>(  
                  ^  
./boost/spirit/home/karma/nonterminal/rule.hpp:230:13: note: in instantiation of function template specialization 'boost::spirit::karma::rule<std::back_insert_iterator<std::basic_string<unsigned int, std::char_traits<unsigned int>, std::allocator<unsigned int> > >, unsigned int (), boost::spirit::unused_type, boost::spirit::unused_type, boost::spirit::unused_type>::define<mpl_::bool_<false>, boost::spirit::terminal<boost::spirit::tag::char_code<boost::spirit::tag::char_, boost::spirit::char_encoding::unicode> > >' requested here  
            define<mpl::false_>(*this, expr, traits::matches<karma::domain, Expr>());  
            ^  
regression_unicode_char.cpp:31:17: note: in instantiation of function template specialization 'boost::spirit::karma::rule<std::back_insert_iterator<std::basic_string<unsigned int, std::char_traits<unsigned int>, std::allocator<unsigned int> > >, unsigned int (), boost::spirit::unused_type, boost::spirit::unused_type, boost::spirit::unused_type>::operator=<boost::spirit::terminal<boost::spirit::tag::char_code<boost::spirit::tag::char_, boost::spirit::char_encoding::unicode> > >' requested here  
        thechar = char_;  
                ^  
regression_unicode_char.cpp:57:66: note: in instantiation of member function 'unicode_char_grammar_<std::back_insert_iterator<std::basic_string<unsigned int, std::char_traits<unsigned int>, std::allocator<unsigned int> > > >::unicode_char_grammar_' requested here  
        unicode_char_grammar_<unicode_back_insert_iterator_type> unichar;  
                                                                 ^  
1 error generated.  
```  
  
MinGW and Cygwin  
----------------  
```  
gcc.compile.c++ bin.v2\libs\spirit\test\karma\karma_regression_unicode_char.test\gcc-8.2.0\debug\cxxstd-11-iso\visibility-hidden\regression_unicode_char.o  
In file included from ./boost/type_index/stl_type_index.hpp:32,  
                 from ./boost/type_index.hpp:29,  
                 from ./boost/variant/variant.hpp:21,  
                 from ./boost/spirit/home/support/info.hpp:14,  
                 from ./boost/spirit/home/karma/nonterminal/grammar.hpp:15,  
                 from libs\spirit\test\karma\regression_unicode_char.cpp:14:  
./boost/spirit/home/karma/detail/output_iterator.hpp: In instantiation of 'void boost::spirit::karma::detail::buffer_sink::output(const T&) [with T = unsigned int]':  
./boost/spirit/home/karma/detail/output_iterator.hpp:292:17:   required from 'bool boost::spirit::karma::detail::buffering_policy::output(const T&) [with T = unsigned int]'  
./boost/spirit/home/karma/detail/output_iterator.hpp:370:56:   required from 'bool boost::spirit::karma::detail::disabling_output_iterator<Buffering, Counting, Tracking>::output(const T&) [with T = unsigned int; Buffering= boost::spirit::karma::detail::buffering_policy; Counting = boost::spirit::karma::detail::counting_policy<boost::spirit::karma::detail::output_iterator<std::back_insert_iterator<std::__cxx11::basic_string<unsigned int> >, mpl_::int_<15>, boost::spirit::unused_type> >; Tracking = boost::spirit::karma::detail::position_policy]'  
./boost/spirit/home/karma/detail/output_iterator.hpp:470:13:   required from 'void boost::spirit::karma::detail::output_iterator<OutputIterator, Properties, Derived>::operator=(const T&) [with T = unsigned int; OutputIterator = std::back_insert_iterator<std::__cxx11::basic_string<unsigned int> >; Properties = mpl_::int_<15>; Derived = boost::spirit::unused_type]'  
./boost/spirit/home/karma/detail/generate_to.hpp:30:15:   required from 'bool boost::spirit::karma::detail::generate_to(OutputIterator&, const Attribute&, CharEncoding, Tag) [with OutputIterator = boost::spirit::karma::detail::output_iterator<std::back_insert_iterator<std::__cxx11::basic_string<unsigned int> >, mpl_::int_<15>, boost::spirit::unused_type>; Attribute = unsigned int; CharEncoding = boost::spirit::char_encoding::unicode; Tag =boost::spirit::unused_type]'  
./boost/spirit/home/karma/char/char_generator.hpp:81:46:   required from 'bool boost::spirit::karma::char_generator<Derived, CharEncoding, Tag, Char, Attr>::generate(OutputIterator&, Context&, const Delimiter&, const Attribute&) const [with OutputIterator = boost::spirit::karma::detail::output_iterator<std::back_insert_iterator<std::__cxx11::basic_string<unsigned int> >, mpl_::int_<15>, boost::spirit::unused_type>; Context = boost::spirit::context<boost::fusion::cons<const unsigned int&, boost::fusion::nil_>, boost::fusion::vector<> >; Delimiter = boost::spirit::unused_type; Attribute = unsigned int; Derived = boost::spirit::karma::any_char<boost::spirit::char_encoding::unicode, boost::spirit::unused_type>; CharEncoding = boost::spirit::char_encoding::unicode; Tag = boost::spirit::unused_type; Char = unsigned int; Attr = unsigned int]'  
./boost/spirit/home/karma/nonterminal/detail/generator_binder.hpp:43:54:   [ skipping 5 instantiation contexts, use -ftemplate-backtrace-limit=0 to disable ]  
./boost/function/function_template.hpp:1073:16:   required from 'boost::function<R(T0, T1, T2)>::function(Functor, typename boost::enable_if_<(! boost::is_integral<Functor>::value), int>::type) [with Functor = boost::spirit::karma::detail::generator_binder<boost::spirit::karma::any_char<boost::spirit::char_encoding::unicode, boost::spirit::unused_type>, mpl_::bool_<false> >; R = bool; T0 = boost::spirit::karma::detail::output_iterator<std::back_insert_iterator<std::__cxx11::basic_string<unsigned int> >, mpl_::int_<15>, boost::spirit::unused_type>&; T1 = boost::spirit::context<boost::fusion::cons<const unsigned int&, boost::fusion::nil_>, boost::fusion::vector<> >&; T2 = const boost::spirit::unused_type&; typename boost::enable_if_<(! boost::is_integral<Functor>::value), int>::type = int]'  
./boost/function/function_template.hpp:1126:5:   required from 'typename boost::enable_if_<(! boost::is_integral<Functor>::value), boost::function<R(T0, T1, T2)>&>::type boost::function<R(T0, T1, T2)>::operator=(Functor) [with Functor = boost::spirit::karma::detail::generator_binder<boost::spirit::karma::any_char<boost::spirit::char_encoding::unicode, boost::spirit::unused_type>, mpl_::bool_<false> >; R = bool; T0 = boost::spirit::karma::detail::output_iterator<std::back_insert_iterator<std::__cxx11::basic_string<unsigned int> >, mpl_::int_<15>, boost::spirit::unused_type>&; T1 = boost::spirit::context<boost::fusion::cons<const unsigned int&, boost::fusion::nil_>, boost::fusion::vector<> >&; T2 = const boost::spirit::unused_type&; typename boost::enable_if_<(! boost::is_integral<Functor>::value), boost::function<R(T0, T1, T2)>&>::type = boost::function<bool(boost::spirit::karma::detail::output_iterator<std::back_insert_iterator<std::__cxx11::basic_string<unsigned int> >, mpl_::int_<15>, boost::spirit::unused_type>&, boost::spirit::context<boost::fusion::cons<const unsigned int&, boost::fusion::nil_>, boost::fusion::vector<> >&, const boost::spirit::unused_type&)>&]'  
./boost/spirit/home/karma/nonterminal/rule.hpp:192:19:   required from 'static void boost::spirit::karma::rule<OutputIterator, T1, T2, T3, T4>::define(boost::spirit::karma::rule<OutputIterator, T1, T2, T3, T4>&, const Expr&, mpl_::true_) [with Auto = mpl_::bool_<false>; Expr = boost::spirit::terminal<boost::spirit::tag::char_code<boost::spirit::tag::char_, boost::spirit::char_encoding::unicode> >; OutputIterator = std::back_insert_iterator<std::__cxx11::basic_string<unsigned int> >; T1 = unsigned int(); T2 = boost::spirit::unused_type; T3 = boost::spirit::unused_type; T4 = boost::spirit::unused_type; mpl_::true_ = mpl_::bool_<true>]'  
./boost/spirit/home/karma/nonterminal/rule.hpp:230:32:   required from 'boost::spirit::karma::rule<OutputIterator, T1, T2, T3, T4>& boost::spirit::karma::rule<OutputIterator, T1, T2, T3, T4>::operator=(const Expr&) [with Expr = boost::spirit::terminal<boost::spirit::tag::char_code<boost::spirit::tag::char_, boost::spirit::char_encoding::unicode> >; OutputIterator = std::back_insert_iterator<std::__cxx11::basic_string<unsigned int> >; T1 = unsigned int(); T2 = boost::spirit::unused_type; T3 = boost::spirit::unused_type; T4 = boost::spirit::unused_type]'  
libs\spirit\test\karma\regression_unicode_char.cpp:31:17:   required from 'unicode_char_grammar_<OutputIterator>::unicode_char_grammar_() [with OutputIterator = std::back_insert_iterator<std::__cxx11::basic_string<unsigned int> >]'  
libs\spirit\test\karma\regression_unicode_char.cpp:57:66:   required from here  
./boost/spirit/home/karma/detail/output_iterator.hpp:225:43: error: static assertion failed: sizeof(T) <= sizeof(buffer_char_type)  
             BOOST_STATIC_ASSERT(sizeof(T) <= sizeof(buffer_char_type));  
                                 ~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~  
./boost/static_assert.hpp:70:55: note: in definition of macro 'BOOST_STATIC_ASSERT'  
 #     define BOOST_STATIC_ASSERT( ... ) static_assert(__VA_ARGS__, #__VA_ARGS__)  
                                                       ^~~~~~~~~~~  
  
    "g++"  -ftemplate-depth-512 -std=c++11 -fvisibility-inlines-hidden -m64 -O0 -fno-inline -Wall -Werror -g -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1  -I"." -I"libs\spirit\test\karma" -c -o "bin.v2\libs\spirit\test\karma\karma_regression_unicode_char.test\gcc-8.2.0\debug\cxxstd-11-iso\visibility-hidden\regression_unicode_char.o" "libs\spirit\test\karma\regression_unicode_char.cpp"  
  
...failed gcc.compile.c++ bin.v2\libs\spirit\test\karma\karma_regression_unicode_char.test\gcc-8.2.0\debug\cxxstd-11-iso\visibility-hidden\regression_unicode_char.o...  
```  
  
</details>

---
