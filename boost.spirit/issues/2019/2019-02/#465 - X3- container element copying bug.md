# #465 - X3: container element copying bug [Closed]

> Username: Xeverous  
> Created at: 2019-02-17 19:46:24 UTC  
> Updated at: 2023-05-08 04:27:40 UTC  
> Closed at: 2019-02-27 16:15:17 UTC  
> Url: https://github.com/boostorg/spirit/issues/465  

Another case of copying AST elements, I have 100+ line template backtrace for this (making minimal example would take some time) but I think that pointing to the code is enough:  
  
`boost/spirit/home/x3/support/traits/container_traits.hpp`  
  
```cpp  
    template <typename Container, typename Enable = void>  
    struct append_container  
    {  
  
    /* [...] */  
  
        template <typename Iterator>  
        static void insert(Container& c, Iterator first, Iterator last, mpl::false_)  
        {  
            c.insert(c.end(), first, last); // attemps to copy into c elements from range [first, last)  
        }  
    }  
```

---

## Comment 1

> Username: djowel  
> Created at: 2019-02-17 22:35:40 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-464519703  

So... It seems I lost the context, if there is one. What's wrong with this code and copying?

---

## Comment 2

> Username: Xeverous  
> Created at: 2019-02-17 23:01:37 UTC  
> Updated at: 2019-02-17 23:03:23 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-464522895  

```  
C:/msys64/mingw64/include/c++/8.2.0/bits/stl_construct.h: In instantiation of 'void std::_Construct(_T1*, _Args&& ...) [with _T1 = fs::parser::ast::value_expression; _Args = {fs::parser::ast::value_expression&}]':  
C:/msys64/mingw64/include/c++/8.2.0/bits/stl_uninitialized.h:83:18:   required from 'static _ForwardIterator std::__uninitialized_copy<_TrivialValueTypes>::__uninit_copy(_InputIterator, _InputIterator, _ForwardIterator) [with _InputIterator = __gnu_cxx::__normal_iterator<fs::parser::ast::value_expression*, std::vector<fs::parser::ast::value_expression> >; _ForwardIterator = fs::parser::ast::value_expression*; bool _TrivialValueTypes = false]'  
C:/msys64/mingw64/include/c++/8.2.0/bits/stl_uninitialized.h:134:15:   required from '_ForwardIterator std::uninitialized_copy(_InputIterator, _InputIterator, _ForwardIterator) [with _InputIterator = __gnu_cxx::__normal_iterator<fs::parser::ast::value_expression*, std::vector<fs::parser::ast::value_expression> >; _ForwardIterator = fs::parser::ast::value_expression*]'  
C:/msys64/mingw64/include/c++/8.2.0/bits/stl_uninitialized.h:289:37:   required from '_ForwardIterator std::__uninitialized_copy_a(_InputIterator, _InputIterator, _ForwardIterator, std::allocator<_Tp>&) [with _InputIterator = __gnu_cxx::__normal_iterator<fs::parser::ast::value_expression*, std::vector<fs::parser::ast::value_expression> >; _ForwardIterator = fs::parser::ast::value_expression*; _Tp = fs::parser::ast::value_expression]'  
C:/msys64/mingw64/include/c++/8.2.0/bits/vector.tcc:702:34:   required from 'void std::vector<_Tp, _Alloc>::_M_range_insert(std::vector<_Tp, _Alloc>::iterator, _ForwardIterator, _ForwardIterator, std::forward_iterator_tag) [with _ForwardIterator = __gnu_cxx::__normal_iterator<fs::parser::ast::value_expression*, std::vector<fs::parser::ast::value_expression> >; _Tp = fs::parser::ast::value_expression; _Alloc = std::allocator<fs::parser::ast::value_expression>; std::vector<_Tp, _Alloc>::iterator = __gnu_cxx::__normal_iterator<fs::parser::ast::value_expression*, std::vector<fs::parser::ast::value_expression> >; typename std::_Vector_base<_Tp, _Alloc>::pointer = fs::parser::ast::value_expression*]'  
C:/msys64/mingw64/include/c++/8.2.0/bits/stl_vector.h:1549:4:   required from 'void std::vector<_Tp, _Alloc>::_M_insert_dispatch(std::vector<_Tp, _Alloc>::iterator, _InputIterator, _InputIterator, std::__false_type) [with _InputIterator = __gnu_cxx::__normal_iterator<fs::parser::ast::value_expression*, std::vector<fs::parser::ast::value_expression> >; _Tp = fs::parser::ast::value_expression; _Alloc = std::allocator<fs::parser::ast::value_expression>; std::vector<_Tp, _Alloc>::iterator = __gnu_cxx::__normal_iterator<fs::parser::ast::value_expression*, std::vector<fs::parser::ast::value_expression> >; typename std::_Vector_base<_Tp, _Alloc>::pointer = fs::parser::ast::value_expression*]'  
C:/msys64/mingw64/include/c++/8.2.0/bits/stl_vector.h:1270:4:   required from 'std::vector<_Tp, _Alloc>::iterator std::vector<_Tp, _Alloc>::insert(std::vector<_Tp, _Alloc>::const_iterator, _InputIterator, _InputIterator) [with _InputIterator = __gnu_cxx::__normal_iterator<fs::parser::ast::value_expression*, std::vector<fs::parser::ast::value_expression> >; <template-parameter-2-2> = void; _Tp = fs::parser::ast::value_expression; _Alloc = std::allocator<fs::parser::ast::value_expression>; std::vector<_Tp, _Alloc>::iterator = __gnu_cxx::__normal_iterator<fs::parser::ast::value_expression*, std::vector<fs::parser::ast::value_expression> >; typename std::_Vector_base<_Tp, _Alloc>::pointer = fs::parser::ast::value_expression*; std::vector<_Tp, _Alloc>::const_iterator = __gnu_cxx::__normal_iterator<const fs::parser::ast::value_expression*, std::vector<fs::parser::ast::value_expression> >; typename __gnu_cxx::__alloc_traits<typename std::_Vector_base<_Tp, _Alloc>::_Tp_alloc_type>::const_pointer = const fs::parser::ast::value_expression*]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/support/traits/container_traits.hpp:176:13:   required from 'static void boost::spirit::x3::traits::append_container<Container, Enable>::insert(Container&, Iterator, Iterator, mpl_::false_) [with Iterator = __gnu_cxx::__normal_iterator<fs::parser::ast::value_expression*, std::vector<fs::parser::ast::value_expression> >; Container = fs::parser::ast::array_expression; Enable = void; mpl_::false_ = mpl_::bool_<false>]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/support/traits/container_traits.hpp:190:19:   required from 'static bool boost::spirit::x3::traits::append_container<Container, Enable>::call(Container&, Iterator, Iterator) [with Iterator = __gnu_cxx::__normal_iterator<fs::parser::ast::value_expression*, std::vector<fs::parser::ast::value_expression> >; Container = fs::parser::ast::array_expression; Enable = void]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/support/traits/container_traits.hpp:198:49:   required from 'bool boost::spirit::x3::traits::append(Container&, Iterator, Iterator) [with Container = fs::parser::ast::array_expression; Iterator = __gnu_cxx::__normal_iterator<fs::parser::ast::value_expression*, std::vector<fs::parser::ast::value_expression> >]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/core/detail/parse_into_container.hpp:265:31:   required from 'static bool boost::spirit::x3::detail::parse_into_container_impl<Parser, Context, RContext, typename boost::enable_if<boost::spirit::x3::traits::handles_container<Parser, Context> >::type>::call(const Parser&, Iterator&, const Iterator&, const Context&, RContext&, Attribute&, mpl_::true_) [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Attribute = fs::parser::ast::array_expression; Parser = boost::spirit::x3::list<boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression>, boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::array_expression; mpl_::true_ = mpl_::bool_<true>]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/core/detail/parse_into_container.hpp:287:24:   required from 'static bool boost::spirit::x3::detail::parse_into_container_impl<Parser, Context, RContext, typename boost::enable_if<boost::spirit::x3::traits::handles_container<Parser, Context> >::type>::call(const Parser&, Iterator&, const Iterator&, const Context&, RContext&, Attribute&) [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Attribute = fs::parser::ast::array_expression; Parser = boost::spirit::x3::list<boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression>, boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::array_expression]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/core/detail/parse_into_container.hpp:299:74:   required from 'bool boost::spirit::x3::detail::parse_into_container(const Parser&, Iterator&, const Iterator&, const Context&, RContext&, Attribute&) [with Parser = boost::spirit::x3::list<boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression>, boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::array_expression; Attribute = fs::parser::ast::array_expression]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/directive/expect.hpp:91:42:   required from 'static bool boost::spirit::x3::detail::parse_into_container_impl<boost::spirit::x3::expect_directive<Subject>, Context, RContext>::call(const boost::spirit::x3::expect_directive<Subject>&, Iterator&, const Iterator&, const Context&, RContext&, Attribute&) [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Attribute = fs::parser::ast::array_expression; Subject = boost::spirit::x3::list<boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression>, boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::array_expression]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/core/detail/parse_into_container.hpp:299:74:   required from 'bool boost::spirit::x3::detail::parse_into_container(const Parser&, Iterator&, const Iterator&, const Context&, RContext&, Attribute&) [with Parser = boost::spirit::x3::expect_directive<boost::spirit::x3::list<boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression>, boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::array_expression; Attribute = fs::parser::ast::array_expression]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/operator/detail/sequence.hpp:348:36:   required from 'typename boost::disable_if_c<pass_sequence_container_attribute<Parser, Context>, bool>::type boost::spirit::x3::detail::parse_sequence_container(const Parser&, Iterator&, const Iterator&, const Context&, RContext&, Attribute&) [with Parser = boost::spirit::x3::expect_directive<boost::spirit::x3::list<boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression>, boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::array_expression; Attribute = fs::parser::ast::array_expression; typename boost::disable_if_c<pass_sequence_container_attribute<Parser, Context>, bool>::type = bool]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/operator/detail/sequence.hpp:360:40:   required from 'bool boost::spirit::x3::detail::parse_sequence(const Parser&, Iterator&, const Iterator&, const Context&, RContext&, Attribute&, boost::spirit::x3::traits::container_attribute) [with Parser = boost::spirit::x3::sequence<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::list<boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression>, boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > > >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::array_expression; Attribute = fs::parser::ast::array_expression]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/operator/sequence.hpp:44:42:   required from 'bool boost::spirit::x3::sequence<Left, Right>::parse(Iterator&, const Iterator&, const Context&, RContext&, Attribute&) const [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::array_expression; Attribute = fs::parser::ast::array_expression; Left = boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>; Right = boost::spirit::x3::expect_directive<boost::spirit::x3::list<boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression>, boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > >]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/core/detail/parse_into_container.hpp:114:69:   required from 'static bool boost::spirit::x3::detail::parse_into_container_base_impl<Parser>::call_synthesize_x(const Parser&, Iterator&, const Iterator&, const Context&, RContext&, Attribute&, mpl_::true_) [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::array_expression; Attribute = fs::parser::ast::array_expression; Parser = boost::spirit::x3::sequence<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::list<boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression>, boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > > >; mpl_::true_ = mpl_::bool_<true>]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/core/detail/parse_into_container.hpp:129:37:   required from 'static bool boost::spirit::x3::detail::parse_into_container_base_impl<Parser>::call_synthesize(const Parser&, Iterator&, const Iterator&, const Context&, RContext&, Attribute&) [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::array_expression; Attribute = fs::parser::ast::array_expression; Parser = boost::spirit::x3::sequence<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::list<boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression>, boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > > >]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/core/detail/parse_into_container.hpp:184:35:   required from 'static bool boost::spirit::x3::detail::parse_into_container_base_impl<Parser>::call_synthesize_dispatch_by_seq(const Parser&, Iterator&, const Iterator&, const Context&, RContext&, Attribute&, mpl_::false_) [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::array_expression; Attribute = fs::parser::ast::array_expression; Parser = boost::spirit::x3::sequence<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::list<boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression>, boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > > >; mpl_::false_ = mpl_::bool_<false>]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/core/detail/parse_into_container.hpp:193:51:   required from 'static bool boost::spirit::x3::detail::parse_into_container_base_impl<Parser>::call(const Parser&, Iterator&, const Iterator&, const Context&, RContext&, Attribute&, mpl_::true_) [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::array_expression; Attribute = fs::parser::ast::array_expression; Parser = boost::spirit::x3::sequence<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::list<boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression>, boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > > >; mpl_::true_ = mpl_::bool_<true>]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/core/detail/parse_into_container.hpp:215:24:   required from 'static bool boost::spirit::x3::detail::parse_into_container_base_impl<Parser>::call(const Parser&, Iterator&, const Iterator&, const Context&, RContext&, Attribute&) [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::array_expression; Attribute = fs::parser::ast::array_expression; Parser = boost::spirit::x3::sequence<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::list<boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression>, boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > > >]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/operator/detail/sequence.hpp:459:69:   required from 'static bool boost::spirit::x3::detail::parse_into_container_impl<boost::spirit::x3::sequence<L, R>, Context, RContext>::call(const parser_type&, Iterator&, const Iterator&, const Context&, RContext&, Attribute&, mpl_::true_) [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Attribute = fs::parser::ast::array_expression; Left = boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>; Right = boost::spirit::x3::expect_directive<boost::spirit::x3::list<boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression>, boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::array_expression; boost::spirit::x3::detail::parse_into_container_impl<boost::spirit::x3::sequence<L, R>, Context, RContext>::parser_type = boost::spirit::x3::sequence<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::list<boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression>, boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > > >; mpl_::true_ = mpl_::bool_<true>]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/operator/detail/sequence.hpp:477:24:   required from 'static bool boost::spirit::x3::detail::parse_into_container_impl<boost::spirit::x3::sequence<L, R>, Context, RContext>::call(const parser_type&, Iterator&, const Iterator&, const Context&, RContext&, Attribute&) [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Attribute = fs::parser::ast::array_expression; Left = boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>; Right = boost::spirit::x3::expect_directive<boost::spirit::x3::list<boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression>, boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::array_expression; boost::spirit::x3::detail::parse_into_container_impl<boost::spirit::x3::sequence<L, R>, Context, RContext>::parser_type = boost::spirit::x3::sequence<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::list<boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression>, boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > > >]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/core/detail/parse_into_container.hpp:299:74:   required from 'bool boost::spirit::x3::detail::parse_into_container(const Parser&, Iterator&, const Iterator&, const Context&, RContext&, Attribute&) [with Parser = boost::spirit::x3::sequence<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::list<boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression>, boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > > >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::array_expression; Attribute = fs::parser::ast::array_expression]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/operator/detail/sequence.hpp:348:36:   required from 'typename boost::disable_if_c<pass_sequence_container_attribute<Parser, Context>, bool>::type boost::spirit::x3::detail::parse_sequence_container(const Parser&, Iterator&, const Iterator&, const Context&, RContext&, Attribute&) [with Parser = boost::spirit::x3::sequence<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::list<boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression>, boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > > >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::array_expression; Attribute = fs::parser::ast::array_expression; typename boost::disable_if_c<pass_sequence_container_attribute<Parser, Context>, bool>::type = bool]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/operator/detail/sequence.hpp:359:37:   required from 'bool boost::spirit::x3::detail::parse_sequence(const Parser&, Iterator&, const Iterator&, const Context&, RContext&, Attribute&, boost::spirit::x3::traits::container_attribute) [with Parser = boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::list<boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression>, boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > > >, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::array_expression; Attribute = fs::parser::ast::array_expression]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/operator/sequence.hpp:44:42:   required from 'bool boost::spirit::x3::sequence<Left, Right>::parse(Iterator&, const Iterator&, const Context&, RContext&, Attribute&) const [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::array_expression; Attribute = fs::parser::ast::array_expression; Left = boost::spirit::x3::sequence<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::list<boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression>, boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > > >; Right = boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> >]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/nonterminal/detail/rule.hpp:206:18:   required from 'static bool boost::spirit::x3::detail::rule_parser<Attribute, ID>::parse_rhs_main(const RHS&, Iterator&, const Iterator&, const Context&, RContext&, ActualAttribute&, mpl_::false_) [with RHS = boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::list<boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression>, boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > > >, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::array_expression; ActualAttribute = fs::parser::ast::array_expression; Attribute = fs::parser::ast::array_expression; ID = fs::parser::array_expression_class; mpl_::false_ = mpl_::bool_<false>]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/nonterminal/detail/rule.hpp:239:42:   required from 'static bool boost::spirit::x3::detail::rule_parser<Attribute, ID>::parse_rhs_main(const RHS&, Iterator&, const Iterator&, const Context&, RContext&, ActualAttribute&, mpl_::true_) [with RHS = boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::list<boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression>, boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > > >, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::array_expression; ActualAttribute = fs::parser::ast::array_expression; Attribute = fs::parser::ast::array_expression; ID = fs::parser::array_expression_class; mpl_::true_ = mpl_::bool_<true>]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/nonterminal/detail/rule.hpp:266:34:   required from 'static bool boost::spirit::x3::detail::rule_parser<Attribute, ID>::parse_rhs_main(const RHS&, Iterator&, const Iterator&, const Context&, RContext&, ActualAttribute&) [with RHS = boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::list<boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression>, boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > > >, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::array_expression; ActualAttribute = fs::parser::ast::array_expression; Attribute = fs::parser::ast::array_expression; ID = fs::parser::array_expression_class]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/nonterminal/detail/rule.hpp:280:34:   required from 'static bool boost::spirit::x3::detail::rule_parser<Attribute, ID>::parse_rhs(const RHS&, Iterator&, const Iterator&, const Context&, RContext&, ActualAttribute&, mpl_::false_) [with RHS = boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::list<boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression>, boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > > >, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::array_expression; ActualAttribute = fs::parser::ast::array_expression; Attribute = fs::parser::ast::array_expression; ID = fs::parser::array_expression_class; mpl_::false_ = mpl_::bool_<false>]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/nonterminal/detail/rule.hpp:329:37:   required from 'static bool boost::spirit::x3::detail::rule_parser<Attribute, ID>::call_rule_definition(const RHS&, const char*, Iterator&, const Iterator&, const Context&, ActualAttribute&, ExplicitAttrPropagation) [with RHS = boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::list<boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression>, boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > > >, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; ActualAttribute = fs::parser::ast::array_expression; ExplicitAttrPropagation = mpl_::bool_<false>; Attribute = fs::parser::ast::array_expression; ID = fs::parser::array_expression_class]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/nonterminal/rule.hpp:62:39:   required from 'bool boost::spirit::x3::rule_definition<ID, RHS, Attribute, force_attribute_>::parse(Iterator&, const Iterator&, const Context&, boost::spirit::x3::unused_type, Attribute_&) const [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; Attribute_ = fs::parser::ast::array_expression; ID = fs::parser::array_expression_class; RHS = boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::list<boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression>, boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > > >, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > >; Attribute = fs::parser::ast::array_expression; bool force_attribute_ = false]'  
G:\Files\C++\workspace_windows\filter_spirit\src/parser/grammar_def.hpp:131:1:   required from 'bool fs::parser::parse_rule(fs::parser::array_expression_type, Iterator&, const Iterator&, const Context&, boost::spirit::x3::rule<fs::parser::array_expression_class, fs::parser::ast::array_expression>::attribute_type&) [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; fs::parser::array_expression_type = boost::spirit::x3::rule<fs::parser::array_expression_class, fs::parser::ast::array_expression>; boost::spirit::x3::rule<fs::parser::array_expression_class, fs::parser::ast::array_expression>::attribute_type = fs::parser::ast::array_expression]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/nonterminal/rule.hpp:123:27:   required from 'bool boost::spirit::x3::rule<ID, Attribute, force_attribute>::parse(Iterator&, const Iterator&, const Context&, boost::spirit::x3::unused_type, Attribute_&) const [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; Attribute_ = fs::parser::ast::array_expression; ID = fs::parser::array_expression_class; Attribute = fs::parser::ast::array_expression; bool force_attribute_ = false]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/operator/detail/alternative.hpp:250:9:   required from 'bool boost::spirit::x3::detail::parse_alternative(const Parser&, Iterator&, const Iterator&, const Context&, RContext&, Attribute&) [with Parser = boost::spirit::x3::rule<fs::parser::array_expression_class, fs::parser::ast::array_expression>; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::value_expression; Attribute = fs::parser::ast::value_expression]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/operator/alternative.hpp:40:44:   required from 'bool boost::spirit::x3::alternative<Left, Right>::parse(Iterator&, const Iterator&, const Context&, RContext&, Attribute&) const [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::value_expression; Attribute = fs::parser::ast::value_expression; Left = boost::spirit::x3::rule<fs::parser::literal_expression_class, fs::parser::ast::literal_expression>; Right = boost::spirit::x3::rule<fs::parser::array_expression_class, fs::parser::ast::array_expression>]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/operator/detail/alternative.hpp:250:9:   required from 'bool boost::spirit::x3::detail::parse_alternative(const Parser&, Iterator&, const Iterator&, const Context&, RContext&, Attribute&) [with Parser = boost::spirit::x3::alternative<boost::spirit::x3::rule<fs::parser::literal_expression_class, fs::parser::ast::literal_expression>, boost::spirit::x3::rule<fs::parser::array_expression_class, fs::parser::ast::array_expression> >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::value_expression; Attribute = fs::parser::ast::value_expression]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/operator/alternative.hpp:39:45:   required from 'bool boost::spirit::x3::alternative<Left, Right>::parse(Iterator&, const Iterator&, const Context&, RContext&, Attribute&) const [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::value_expression; Attribute = fs::parser::ast::value_expression; Left = boost::spirit::x3::alternative<boost::spirit::x3::rule<fs::parser::literal_expression_class, fs::parser::ast::literal_expression>, boost::spirit::x3::rule<fs::parser::array_expression_class, fs::parser::ast::array_expression> >; Right = boost::spirit::x3::rule<fs::parser::function_call_class, fs::parser::ast::function_call>]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/operator/detail/alternative.hpp:250:9:   required from 'bool boost::spirit::x3::detail::parse_alternative(const Parser&, Iterator&, const Iterator&, const Context&, RContext&, Attribute&) [with Parser = boost::spirit::x3::alternative<boost::spirit::x3::alternative<boost::spirit::x3::rule<fs::parser::literal_expression_class, fs::parser::ast::literal_expression>, boost::spirit::x3::rule<fs::parser::array_expression_class, fs::parser::ast::array_expression> >, boost::spirit::x3::rule<fs::parser::function_call_class, fs::parser::ast::function_call> >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::value_expression; Attribute = fs::parser::ast::value_expression]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/operator/alternative.hpp:39:45:   required from 'bool boost::spirit::x3::alternative<Left, Right>::parse(Iterator&, const Iterator&, const Context&, RContext&, Attribute&) const [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::value_expression; Attribute = fs::parser::ast::value_expression; Left = boost::spirit::x3::alternative<boost::spirit::x3::alternative<boost::spirit::x3::rule<fs::parser::literal_expression_class, fs::parser::ast::literal_expression>, boost::spirit::x3::rule<fs::parser::array_expression_class, fs::parser::ast::array_expression> >, boost::spirit::x3::rule<fs::parser::function_call_class, fs::parser::ast::function_call> >; Right = boost::spirit::x3::rule<fs::parser::identifier_class, fs::parser::ast::identifier>]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/nonterminal/detail/rule.hpp:206:18:   required from 'static bool boost::spirit::x3::detail::rule_parser<Attribute, ID>::parse_rhs_main(const RHS&, Iterator&, const Iterator&, const Context&, RContext&, ActualAttribute&, mpl_::false_) [with RHS = boost::spirit::x3::alternative<boost::spirit::x3::alternative<boost::spirit::x3::alternative<boost::spirit::x3::rule<fs::parser::literal_expression_class, fs::parser::ast::literal_expression>, boost::spirit::x3::rule<fs::parser::array_expression_class, fs::parser::ast::array_expression> >, boost::spirit::x3::rule<fs::parser::function_call_class, fs::parser::ast::function_call> >, boost::spirit::x3::rule<fs::parser::identifier_class, fs::parser::ast::identifier> >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::value_expression; ActualAttribute = fs::parser::ast::value_expression; Attribute = fs::parser::ast::value_expression; ID = fs::parser::value_expression_class; mpl_::false_ = mpl_::bool_<false>]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/nonterminal/detail/rule.hpp:239:42:   required from 'static bool boost::spirit::x3::detail::rule_parser<Attribute, ID>::parse_rhs_main(const RHS&, Iterator&, const Iterator&, const Context&, RContext&, ActualAttribute&, mpl_::true_) [with RHS = boost::spirit::x3::alternative<boost::spirit::x3::alternative<boost::spirit::x3::alternative<boost::spirit::x3::rule<fs::parser::literal_expression_class, fs::parser::ast::literal_expression>, boost::spirit::x3::rule<fs::parser::array_expression_class, fs::parser::ast::array_expression> >, boost::spirit::x3::rule<fs::parser::function_call_class, fs::parser::ast::function_call> >, boost::spirit::x3::rule<fs::parser::identifier_class, fs::parser::ast::identifier> >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::value_expression; ActualAttribute = fs::parser::ast::value_expression; Attribute = fs::parser::ast::value_expression; ID = fs::parser::value_expression_class; mpl_::true_ = mpl_::bool_<true>]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/nonterminal/detail/rule.hpp:266:34:   required from 'static bool boost::spirit::x3::detail::rule_parser<Attribute, ID>::parse_rhs_main(const RHS&, Iterator&, const Iterator&, const Context&, RContext&, ActualAttribute&) [with RHS = boost::spirit::x3::alternative<boost::spirit::x3::alternative<boost::spirit::x3::alternative<boost::spirit::x3::rule<fs::parser::literal_expression_class, fs::parser::ast::literal_expression>, boost::spirit::x3::rule<fs::parser::array_expression_class, fs::parser::ast::array_expression> >, boost::spirit::x3::rule<fs::parser::function_call_class, fs::parser::ast::function_call> >, boost::spirit::x3::rule<fs::parser::identifier_class, fs::parser::ast::identifier> >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::value_expression; ActualAttribute = fs::parser::ast::value_expression; Attribute = fs::parser::ast::value_expression; ID = fs::parser::value_expression_class]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/nonterminal/detail/rule.hpp:280:34:   required from 'static bool boost::spirit::x3::detail::rule_parser<Attribute, ID>::parse_rhs(const RHS&, Iterator&, const Iterator&, const Context&, RContext&, ActualAttribute&, mpl_::false_) [with RHS = boost::spirit::x3::alternative<boost::spirit::x3::alternative<boost::spirit::x3::alternative<boost::spirit::x3::rule<fs::parser::literal_expression_class, fs::parser::ast::literal_expression>, boost::spirit::x3::rule<fs::parser::array_expression_class, fs::parser::ast::array_expression> >, boost::spirit::x3::rule<fs::parser::function_call_class, fs::parser::ast::function_call> >, boost::spirit::x3::rule<fs::parser::identifier_class, fs::parser::ast::identifier> >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::value_expression; ActualAttribute = fs::parser::ast::value_expression; Attribute = fs::parser::ast::value_expression; ID = fs::parser::value_expression_class; mpl_::false_ = mpl_::bool_<false>]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/nonterminal/detail/rule.hpp:329:37:   required from 'static bool boost::spirit::x3::detail::rule_parser<Attribute, ID>::call_rule_definition(const RHS&, const char*, Iterator&, const Iterator&, const Context&, ActualAttribute&, ExplicitAttrPropagation) [with RHS = boost::spirit::x3::alternative<boost::spirit::x3::alternative<boost::spirit::x3::alternative<boost::spirit::x3::rule<fs::parser::literal_expression_class, fs::parser::ast::literal_expression>, boost::spirit::x3::rule<fs::parser::array_expression_class, fs::parser::ast::array_expression> >, boost::spirit::x3::rule<fs::parser::function_call_class, fs::parser::ast::function_call> >, boost::spirit::x3::rule<fs::parser::identifier_class, fs::parser::ast::identifier> >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; ActualAttribute = fs::parser::ast::value_expression; ExplicitAttrPropagation = mpl_::bool_<false>; Attribute = fs::parser::ast::value_expression; ID = fs::parser::value_expression_class]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/nonterminal/rule.hpp:62:39:   required from 'bool boost::spirit::x3::rule_definition<ID, RHS, Attribute, force_attribute_>::parse(Iterator&, const Iterator&, const Context&, boost::spirit::x3::unused_type, Attribute_&) const [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; Attribute_ = fs::parser::ast::value_expression; ID = fs::parser::value_expression_class; RHS = boost::spirit::x3::alternative<boost::spirit::x3::alternative<boost::spirit::x3::alternative<boost::spirit::x3::rule<fs::parser::literal_expression_class, fs::parser::ast::literal_expression>, boost::spirit::x3::rule<fs::parser::array_expression_class, fs::parser::ast::array_expression> >, boost::spirit::x3::rule<fs::parser::function_call_class, fs::parser::ast::function_call> >, boost::spirit::x3::rule<fs::parser::identifier_class, fs::parser::ast::identifier> >; Attribute = fs::parser::ast::value_expression; bool force_attribute_ = false]'  
G:\Files\C++\workspace_windows\filter_spirit\src/parser/grammar_def.hpp:138:1:   required from 'bool fs::parser::parse_rule(fs::parser::value_expression_type, Iterator&, const Iterator&, const Context&, boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression>::attribute_type&) [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; fs::parser::value_expression_type = boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression>; boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression>::attribute_type = fs::parser::ast::value_expression]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/nonterminal/rule.hpp:123:27:   required from 'bool boost::spirit::x3::rule<ID, Attribute, force_attribute>::parse(Iterator&, const Iterator&, const Context&, boost::spirit::x3::unused_type, Attribute_&) const [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; Attribute_ = fs::parser::ast::value_expression; ID = fs::parser::value_expression_class; Attribute = fs::parser::ast::value_expression; bool force_attribute_ = false]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/directive/expect.hpp:54:18:   required from 'bool boost::spirit::x3::expect_directive<Subject>::parse(Iterator&, const Iterator&, const Context&, RContext&, Attribute&) const [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::constant_definition; Attribute = fs::parser::ast::value_expression; Subject = boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression>]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/operator/detail/sequence.hpp:319:13:   required from 'bool boost::spirit::x3::detail::parse_sequence(const Parser&, Iterator&, const Iterator&, const Context&, RContext&, Attribute&, AttributeCategory) [with Parser = boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char*, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::rule<fs::parser::identifier_class, fs::parser::ast::identifier> > >, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > >, boost::spirit::x3::expect_directive<boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression> > >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::constant_definition; Attribute = fs::parser::ast::constant_definition; AttributeCategory = boost::spirit::x3::traits::tuple_attribute]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/operator/sequence.hpp:44:42:   required from 'bool boost::spirit::x3::sequence<Left, Right>::parse(Iterator&, const Iterator&, const Context&, RContext&, Attribute&) const [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::constant_definition; Attribute = fs::parser::ast::constant_definition; Left = boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char*, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::rule<fs::parser::identifier_class, fs::parser::ast::identifier> > >, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > >; Right = boost::spirit::x3::expect_directive<boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression> >]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/nonterminal/detail/rule.hpp:206:18:   required from 'static bool boost::spirit::x3::detail::rule_parser<Attribute, ID>::parse_rhs_main(const RHS&, Iterator&, const Iterator&, const Context&, RContext&, ActualAttribute&, mpl_::false_) [with RHS = boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char*, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::rule<fs::parser::identifier_class, fs::parser::ast::identifier> > >, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > >, boost::spirit::x3::expect_directive<boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression> > >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::constant_definition; ActualAttribute = fs::parser::ast::constant_definition; Attribute = fs::parser::ast::constant_definition; ID = fs::parser::constant_definition_class; mpl_::false_ = mpl_::bool_<false>]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/nonterminal/detail/rule.hpp:239:42:   required from 'static bool boost::spirit::x3::detail::rule_parser<Attribute, ID>::parse_rhs_main(const RHS&, Iterator&, const Iterator&, const Context&, RContext&, ActualAttribute&, mpl_::true_) [with RHS = boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char*, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::rule<fs::parser::identifier_class, fs::parser::ast::identifier> > >, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > >, boost::spirit::x3::expect_directive<boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression> > >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::constant_definition; ActualAttribute = fs::parser::ast::constant_definition; Attribute = fs::parser::ast::constant_definition; ID = fs::parser::constant_definition_class; mpl_::true_ = mpl_::bool_<true>]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/nonterminal/detail/rule.hpp:266:34:   required from 'static bool boost::spirit::x3::detail::rule_parser<Attribute, ID>::parse_rhs_main(const RHS&, Iterator&, const Iterator&, const Context&, RContext&, ActualAttribute&) [with RHS = boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char*, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::rule<fs::parser::identifier_class, fs::parser::ast::identifier> > >, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > >, boost::spirit::x3::expect_directive<boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression> > >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::constant_definition; ActualAttribute = fs::parser::ast::constant_definition; Attribute = fs::parser::ast::constant_definition; ID = fs::parser::constant_definition_class]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/nonterminal/detail/rule.hpp:280:34:   required from 'static bool boost::spirit::x3::detail::rule_parser<Attribute, ID>::parse_rhs(const RHS&, Iterator&, const Iterator&, const Context&, RContext&, ActualAttribute&, mpl_::false_) [with RHS = boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char*, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::rule<fs::parser::identifier_class, fs::parser::ast::identifier> > >, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > >, boost::spirit::x3::expect_directive<boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression> > >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::constant_definition; ActualAttribute = fs::parser::ast::constant_definition; Attribute = fs::parser::ast::constant_definition; ID = fs::parser::constant_definition_class; mpl_::false_ = mpl_::bool_<false>]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/nonterminal/detail/rule.hpp:329:37:   required from 'static bool boost::spirit::x3::detail::rule_parser<Attribute, ID>::call_rule_definition(const RHS&, const char*, Iterator&, const Iterator&, const Context&, ActualAttribute&, ExplicitAttrPropagation) [with RHS = boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char*, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::rule<fs::parser::identifier_class, fs::parser::ast::identifier> > >, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > >, boost::spirit::x3::expect_directive<boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression> > >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; ActualAttribute = fs::parser::ast::constant_definition; ExplicitAttrPropagation = mpl_::bool_<false>; Attribute = fs::parser::ast::constant_definition; ID = fs::parser::constant_definition_class]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/nonterminal/rule.hpp:62:39:   required from 'bool boost::spirit::x3::rule_definition<ID, RHS, Attribute, force_attribute_>::parse(Iterator&, const Iterator&, const Context&, boost::spirit::x3::unused_type, Attribute_&) const [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; Attribute_ = fs::parser::ast::constant_definition; ID = fs::parser::constant_definition_class; RHS = boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::literal_string<const char*, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>, boost::spirit::x3::expect_directive<boost::spirit::x3::rule<fs::parser::identifier_class, fs::parser::ast::identifier> > >, boost::spirit::x3::expect_directive<boost::spirit::x3::literal_char<boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type> > >, boost::spirit::x3::expect_directive<boost::spirit::x3::rule<fs::parser::value_expression_class, fs::parser::ast::value_expression> > >; Attribute = fs::parser::ast::constant_definition; bool force_attribute_ = false]'  
G:\Files\C++\workspace_windows\filter_spirit\src/parser/grammar_def.hpp:144:1:   required from 'bool fs::parser::parse_rule(fs::parser::constant_definition_type, Iterator&, const Iterator&, const Context&, boost::spirit::x3::rule<fs::parser::constant_definition_class, fs::parser::ast::constant_definition>::attribute_type&) [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; fs::parser::constant_definition_type = boost::spirit::x3::rule<fs::parser::constant_definition_class, fs::parser::ast::constant_definition>; boost::spirit::x3::rule<fs::parser::constant_definition_class, fs::parser::ast::constant_definition>::attribute_type = fs::parser::ast::constant_definition]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/nonterminal/rule.hpp:123:27:   required from 'bool boost::spirit::x3::rule<ID, Attribute, force_attribute>::parse(Iterator&, const Iterator&, const Context&, boost::spirit::x3::unused_type, Attribute_&) const [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; Attribute_ = fs::parser::ast::constant_definition; ID = fs::parser::constant_definition_class; Attribute = fs::parser::ast::constant_definition; bool force_attribute_ = false]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/core/detail/parse_into_container.hpp:98:17:   required from 'static bool boost::spirit::x3::detail::parse_into_container_base_impl<Parser>::call_synthesize_x(const Parser&, Iterator&, const Iterator&, const Context&, RContext&, Attribute&, mpl_::false_) [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::filter_structure; Attribute = std::vector<fs::parser::ast::constant_definition>; Parser = boost::spirit::x3::rule<fs::parser::constant_definition_class, fs::parser::ast::constant_definition>; mpl_::false_ = mpl_::bool_<false>]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/core/detail/parse_into_container.hpp:129:37:   required from 'static bool boost::spirit::x3::detail::parse_into_container_base_impl<Parser>::call_synthesize(const Parser&, Iterator&, const Iterator&, const Context&, RContext&, Attribute&) [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::filter_structure; Attribute = std::vector<fs::parser::ast::constant_definition>; Parser = boost::spirit::x3::rule<fs::parser::constant_definition_class, fs::parser::ast::constant_definition>]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/core/detail/parse_into_container.hpp:184:35:   required from 'static bool boost::spirit::x3::detail::parse_into_container_base_impl<Parser>::call_synthesize_dispatch_by_seq(const Parser&, Iterator&, const Iterator&, const Context&, RContext&, Attribute&, mpl_::false_) [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::filter_structure; Attribute = std::vector<fs::parser::ast::constant_definition>; Parser = boost::spirit::x3::rule<fs::parser::constant_definition_class, fs::parser::ast::constant_definition>; mpl_::false_ = mpl_::bool_<false>]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/core/detail/parse_into_container.hpp:193:51:   required from 'static bool boost::spirit::x3::detail::parse_into_container_base_impl<Parser>::call(const Parser&, Iterator&, const Iterator&, const Context&, RContext&, Attribute&, mpl_::true_) [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::filter_structure; Attribute = std::vector<fs::parser::ast::constant_definition>; Parser = boost::spirit::x3::rule<fs::parser::constant_definition_class, fs::parser::ast::constant_definition>; mpl_::true_ = mpl_::bool_<true>]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/core/detail/parse_into_container.hpp:215:24:   required from 'static bool boost::spirit::x3::detail::parse_into_container_base_impl<Parser>::call(const Parser&, Iterator&, const Iterator&, const Context&, RContext&, Attribute&) [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::filter_structure; Attribute = std::vector<fs::parser::ast::constant_definition>; Parser = boost::spirit::x3::rule<fs::parser::constant_definition_class, fs::parser::ast::constant_definition>]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/core/detail/parse_into_container.hpp:299:74:   required from 'bool boost::spirit::x3::detail::parse_into_container(const Parser&, Iterator&, const Iterator&, const Context&, RContext&, Attribute&) [with Parser = boost::spirit::x3::rule<fs::parser::constant_definition_class, fs::parser::ast::constant_definition>; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::filter_structure; Attribute = std::vector<fs::parser::ast::constant_definition>]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/operator/kleene.hpp:32:48:   required from 'bool boost::spirit::x3::kleene<Subject>::parse(Iterator&, const Iterator&, const Context&, RContext&, Attribute&) const [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::filter_structure; Attribute = std::vector<fs::parser::ast::constant_definition>; Subject = boost::spirit::x3::rule<fs::parser::constant_definition_class, fs::parser::ast::constant_definition>]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/directive/expect.hpp:54:18:   required from 'bool boost::spirit::x3::expect_directive<Subject>::parse(Iterator&, const Iterator&, const Context&, RContext&, Attribute&) const [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::filter_structure; Attribute = std::vector<fs::parser::ast::constant_definition>; Subject = boost::spirit::x3::kleene<boost::spirit::x3::rule<fs::parser::constant_definition_class, fs::parser::ast::constant_definition> >]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/operator/detail/sequence.hpp:319:13:   required from 'bool boost::spirit::x3::detail::parse_sequence(const Parser&, Iterator&, const Iterator&, const Context&, RContext&, Attribute&, AttributeCategory) [with Parser = boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::rule<fs::parser::version_requirement_statement_class, fs::parser::ast::version_literal>, boost::spirit::x3::expect_directive<boost::spirit::x3::rule<fs::parser::config_class, fs::parser::ast::config> > >, boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::rule<fs::parser::constant_definition_class, fs::parser::ast::constant_definition> > > >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::filter_structure; Attribute = boost::fusion::iterator_range<boost::fusion::basic_iterator<boost::fusion::struct_iterator_tag, boost::fusion::random_access_traversal_tag, fs::parser::ast::filter_structure, 0>, boost::fusion::basic_iterator<boost::fusion::struct_iterator_tag, boost::fusion::random_access_traversal_tag, fs::parser::ast::filter_structure, 3> >; AttributeCategory = boost::spirit::x3::traits::tuple_attribute]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/operator/sequence.hpp:44:42:   required from 'bool boost::spirit::x3::sequence<Left, Right>::parse(Iterator&, const Iterator&, const Context&, RContext&, Attribute&) const [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::filter_structure; Attribute = boost::fusion::iterator_range<boost::fusion::basic_iterator<boost::fusion::struct_iterator_tag, boost::fusion::random_access_traversal_tag, fs::parser::ast::filter_structure, 0>, boost::fusion::basic_iterator<boost::fusion::struct_iterator_tag, boost::fusion::random_access_traversal_tag, fs::parser::ast::filter_structure, 3> >; Left = boost::spirit::x3::sequence<boost::spirit::x3::rule<fs::parser::version_requirement_statement_class, fs::parser::ast::version_literal>, boost::spirit::x3::expect_directive<boost::spirit::x3::rule<fs::parser::config_class, fs::parser::ast::config> > >; Right = boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::rule<fs::parser::constant_definition_class, fs::parser::ast::constant_definition> > >]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/operator/detail/sequence.hpp:319:13:   required from 'bool boost::spirit::x3::detail::parse_sequence(const Parser&, Iterator&, const Iterator&, const Context&, RContext&, Attribute&, AttributeCategory) [with Parser = boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::rule<fs::parser::version_requirement_statement_class, fs::parser::ast::version_literal>, boost::spirit::x3::expect_directive<boost::spirit::x3::rule<fs::parser::config_class, fs::parser::ast::config> > >, boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::rule<fs::parser::constant_definition_class, fs::parser::ast::constant_definition> > > >, boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::rule<fs::parser::statement_class, fs::parser::ast::statement> > > >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::filter_structure; Attribute = fs::parser::ast::filter_structure; AttributeCategory = boost::spirit::x3::traits::tuple_attribute]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/operator/sequence.hpp:44:42:   required from 'bool boost::spirit::x3::sequence<Left, Right>::parse(Iterator&, const Iterator&, const Context&, RContext&, Attribute&) const [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::filter_structure; Attribute = fs::parser::ast::filter_structure; Left = boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::rule<fs::parser::version_requirement_statement_class, fs::parser::ast::version_literal>, boost::spirit::x3::expect_directive<boost::spirit::x3::rule<fs::parser::config_class, fs::parser::ast::config> > >, boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::rule<fs::parser::constant_definition_class, fs::parser::ast::constant_definition> > > >; Right = boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::rule<fs::parser::statement_class, fs::parser::ast::statement> > >]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/nonterminal/detail/rule.hpp:206:18:   required from 'static bool boost::spirit::x3::detail::rule_parser<Attribute, ID>::parse_rhs_main(const RHS&, Iterator&, const Iterator&, const Context&, RContext&, ActualAttribute&, mpl_::false_) [with RHS = boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::rule<fs::parser::version_requirement_statement_class, fs::parser::ast::version_literal>, boost::spirit::x3::expect_directive<boost::spirit::x3::rule<fs::parser::config_class, fs::parser::ast::config> > >, boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::rule<fs::parser::constant_definition_class, fs::parser::ast::constant_definition> > > >, boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::rule<fs::parser::statement_class, fs::parser::ast::statement> > > >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::filter_structure; ActualAttribute = fs::parser::ast::filter_structure; Attribute = fs::parser::ast::filter_structure; ID = fs::parser::filter_structure_class; mpl_::false_ = mpl_::bool_<false>]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/nonterminal/detail/rule.hpp:239:42:   required from 'static bool boost::spirit::x3::detail::rule_parser<Attribute, ID>::parse_rhs_main(const RHS&, Iterator&, const Iterator&, const Context&, RContext&, ActualAttribute&, mpl_::true_) [with RHS = boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::rule<fs::parser::version_requirement_statement_class, fs::parser::ast::version_literal>, boost::spirit::x3::expect_directive<boost::spirit::x3::rule<fs::parser::config_class, fs::parser::ast::config> > >, boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::rule<fs::parser::constant_definition_class, fs::parser::ast::constant_definition> > > >, boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::rule<fs::parser::statement_class, fs::parser::ast::statement> > > >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::filter_structure; ActualAttribute = fs::parser::ast::filter_structure; Attribute = fs::parser::ast::filter_structure; ID = fs::parser::filter_structure_class; mpl_::true_ = mpl_::bool_<true>]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/nonterminal/detail/rule.hpp:266:34:   required from 'static bool boost::spirit::x3::detail::rule_parser<Attribute, ID>::parse_rhs_main(const RHS&, Iterator&, const Iterator&, const Context&, RContext&, ActualAttribute&) [with RHS = boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::rule<fs::parser::version_requirement_statement_class, fs::parser::ast::version_literal>, boost::spirit::x3::expect_directive<boost::spirit::x3::rule<fs::parser::config_class, fs::parser::ast::config> > >, boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::rule<fs::parser::constant_definition_class, fs::parser::ast::constant_definition> > > >, boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::rule<fs::parser::statement_class, fs::parser::ast::statement> > > >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::filter_structure; ActualAttribute = fs::parser::ast::filter_structure; Attribute = fs::parser::ast::filter_structure; ID = fs::parser::filter_structure_class]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/nonterminal/detail/rule.hpp:280:34:   required from 'static bool boost::spirit::x3::detail::rule_parser<Attribute, ID>::parse_rhs(const RHS&, Iterator&, const Iterator&, const Context&, RContext&, ActualAttribute&, mpl_::false_) [with RHS = boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::rule<fs::parser::version_requirement_statement_class, fs::parser::ast::version_literal>, boost::spirit::x3::expect_directive<boost::spirit::x3::rule<fs::parser::config_class, fs::parser::ast::config> > >, boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::rule<fs::parser::constant_definition_class, fs::parser::ast::constant_definition> > > >, boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::rule<fs::parser::statement_class, fs::parser::ast::statement> > > >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::filter_structure; ActualAttribute = fs::parser::ast::filter_structure; Attribute = fs::parser::ast::filter_structure; ID = fs::parser::filter_structure_class; mpl_::false_ = mpl_::bool_<false>]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/nonterminal/detail/rule.hpp:329:37:   required from 'static bool boost::spirit::x3::detail::rule_parser<Attribute, ID>::call_rule_definition(const RHS&, const char*, Iterator&, const Iterator&, const Context&, ActualAttribute&, ExplicitAttrPropagation) [with RHS = boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::rule<fs::parser::version_requirement_statement_class, fs::parser::ast::version_literal>, boost::spirit::x3::expect_directive<boost::spirit::x3::rule<fs::parser::config_class, fs::parser::ast::config> > >, boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::rule<fs::parser::constant_definition_class, fs::parser::ast::constant_definition> > > >, boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::rule<fs::parser::statement_class, fs::parser::ast::statement> > > >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; ActualAttribute = fs::parser::ast::filter_structure; ExplicitAttrPropagation = mpl_::bool_<false>; Attribute = fs::parser::ast::filter_structure; ID = fs::parser::filter_structure_class]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/nonterminal/rule.hpp:62:39:   required from 'bool boost::spirit::x3::rule_definition<ID, RHS, Attribute, force_attribute_>::parse(Iterator&, const Iterator&, const Context&, boost::spirit::x3::unused_type, Attribute_&) const [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; Attribute_ = fs::parser::ast::filter_structure; ID = fs::parser::filter_structure_class; RHS = boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::sequence<boost::spirit::x3::rule<fs::parser::version_requirement_statement_class, fs::parser::ast::version_literal>, boost::spirit::x3::expect_directive<boost::spirit::x3::rule<fs::parser::config_class, fs::parser::ast::config> > >, boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::rule<fs::parser::constant_definition_class, fs::parser::ast::constant_definition> > > >, boost::spirit::x3::expect_directive<boost::spirit::x3::kleene<boost::spirit::x3::rule<fs::parser::statement_class, fs::parser::ast::statement> > > >; Attribute = fs::parser::ast::filter_structure; bool force_attribute_ = false]'  
G:\Files\C++\workspace_windows\filter_spirit\src/parser/grammar_def.hpp:198:1:   required from 'bool fs::parser::parse_rule(fs::parser::filter_structure_type, Iterator&, const Iterator&, const Context&, boost::spirit::x3::rule<fs::parser::filter_structure_class, fs::parser::ast::filter_structure>::attribute_type&) [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; fs::parser::filter_structure_type = boost::spirit::x3::rule<fs::parser::filter_structure_class, fs::parser::ast::filter_structure>; boost::spirit::x3::rule<fs::parser::filter_structure_class, fs::parser::ast::filter_structure>::attribute_type = fs::parser::ast::filter_structure]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/nonterminal/rule.hpp:123:27:   required from 'bool boost::spirit::x3::rule<ID, Attribute, force_attribute>::parse(Iterator&, const Iterator&, const Context&, boost::spirit::x3::unused_type, Attribute_&) const [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; Attribute_ = fs::parser::ast::filter_structure; ID = fs::parser::filter_structure_class; Attribute = fs::parser::ast::filter_structure; bool force_attribute_ = false]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/operator/detail/sequence.hpp:319:13:   required from 'bool boost::spirit::x3::detail::parse_sequence(const Parser&, Iterator&, const Iterator&, const Context&, RContext&, Attribute&, AttributeCategory) [with Parser = boost::spirit::x3::sequence<boost::spirit::x3::rule<fs::parser::filter_structure_class, fs::parser::ast::filter_structure>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser> >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::filter_structure; Attribute = fs::parser::ast::filter_structure; AttributeCategory = boost::spirit::x3::traits::tuple_attribute]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/operator/sequence.hpp:44:42:   required from 'bool boost::spirit::x3::sequence<Left, Right>::parse(Iterator&, const Iterator&, const Context&, RContext&, Attribute&) const [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::filter_structure; Attribute = fs::parser::ast::filter_structure; Left = boost::spirit::x3::rule<fs::parser::filter_structure_class, fs::parser::ast::filter_structure>; Right = boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser>]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/nonterminal/detail/rule.hpp:206:18:   required from 'static bool boost::spirit::x3::detail::rule_parser<Attribute, ID>::parse_rhs_main(const RHS&, Iterator&, const Iterator&, const Context&, RContext&, ActualAttribute&, mpl_::false_) [with RHS = boost::spirit::x3::sequence<boost::spirit::x3::rule<fs::parser::filter_structure_class, fs::parser::ast::filter_structure>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser> >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::filter_structure; ActualAttribute = fs::parser::ast::filter_structure; Attribute = fs::parser::ast::filter_structure; ID = fs::parser::grammar_class; mpl_::false_ = mpl_::bool_<false>]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/nonterminal/detail/rule.hpp:239:42:   required from 'static bool boost::spirit::x3::detail::rule_parser<Attribute, ID>::parse_rhs_main(const RHS&, Iterator&, const Iterator&, const Context&, RContext&, ActualAttribute&, mpl_::true_) [with RHS = boost::spirit::x3::sequence<boost::spirit::x3::rule<fs::parser::filter_structure_class, fs::parser::ast::filter_structure>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser> >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::filter_structure; ActualAttribute = fs::parser::ast::filter_structure; Attribute = fs::parser::ast::filter_structure; ID = fs::parser::grammar_class; mpl_::true_ = mpl_::bool_<true>]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/nonterminal/detail/rule.hpp:266:34:   required from 'static bool boost::spirit::x3::detail::rule_parser<Attribute, ID>::parse_rhs_main(const RHS&, Iterator&, const Iterator&, const Context&, RContext&, ActualAttribute&) [with RHS = boost::spirit::x3::sequence<boost::spirit::x3::rule<fs::parser::filter_structure_class, fs::parser::ast::filter_structure>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser> >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::filter_structure; ActualAttribute = fs::parser::ast::filter_structure; Attribute = fs::parser::ast::filter_structure; ID = fs::parser::grammar_class]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/nonterminal/detail/rule.hpp:280:34:   required from 'static bool boost::spirit::x3::detail::rule_parser<Attribute, ID>::parse_rhs(const RHS&, Iterator&, const Iterator&, const Context&, RContext&, ActualAttribute&, mpl_::false_) [with RHS = boost::spirit::x3::sequence<boost::spirit::x3::rule<fs::parser::filter_structure_class, fs::parser::ast::filter_structure>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser> >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; RContext = fs::parser::ast::filter_structure; ActualAttribute = fs::parser::ast::filter_structure; Attribute = fs::parser::ast::filter_structure; ID = fs::parser::grammar_class; mpl_::false_ = mpl_::bool_<false>]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/nonterminal/detail/rule.hpp:329:37:   required from 'static bool boost::spirit::x3::detail::rule_parser<Attribute, ID>::call_rule_definition(const RHS&, const char*, Iterator&, const Iterator&, const Context&, ActualAttribute&, ExplicitAttrPropagation) [with RHS = boost::spirit::x3::sequence<boost::spirit::x3::rule<fs::parser::filter_structure_class, fs::parser::ast::filter_structure>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser> >; Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; ActualAttribute = fs::parser::ast::filter_structure; ExplicitAttrPropagation = mpl_::bool_<false>; Attribute = fs::parser::ast::filter_structure; ID = fs::parser::grammar_class]'  
G:\Files\C++\repos\spirit\include/boost/spirit/home/x3/nonterminal/rule.hpp:62:39:   required from 'bool boost::spirit::x3::rule_definition<ID, RHS, Attribute, force_attribute_>::parse(Iterator&, const Iterator&, const Context&, boost::spirit::x3::unused_type, Attribute_&) const [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; Attribute_ = fs::parser::ast::filter_structure; ID = fs::parser::grammar_class; RHS = boost::spirit::x3::sequence<boost::spirit::x3::rule<fs::parser::filter_structure_class, fs::parser::ast::filter_structure>, boost::spirit::x3::expect_directive<boost::spirit::x3::eoi_parser> >; Attribute = fs::parser::ast::filter_structure; bool force_attribute_ = false]'  
G:\Files\C++\workspace_windows\filter_spirit\src/parser/grammar_def.hpp:202:1:   required from 'bool fs::parser::parse_rule(fs::parser::grammar_type, Iterator&, const Iterator&, const Context&, boost::spirit::x3::rule<fs::parser::grammar_class, fs::parser::ast::filter_structure>::attribute_type&) [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Context = boost::spirit::x3::context<fs::parser::error_holder_tag, std::reference_wrapper<std::vector<fs::parser::parse_error> >, boost::spirit::x3::context<fs::parser::position_cache_tag, std::reference_wrapper<boost::spirit::x3::position_cache<std::vector<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > > >, boost::spirit::x3::context<boost::spirit::x3::skipper_tag, const boost::spirit::x3::rule<fs::parser::whitespace_class>, boost::spirit::x3::unused_type> > >; fs::parser::grammar_type = boost::spirit::x3::rule<fs::parser::grammar_class, fs::parser::ast::filter_structure>; boost::spirit::x3::rule<fs::parser::grammar_class, fs::parser::ast::filter_structure>::attribute_type = fs::parser::ast::filter_structure]'  
../src/parser/grammar.cpp:13:1:   required from here  
C:/msys64/mingw64/include/c++/8.2.0/bits/stl_construct.h:75:7: error: use of deleted function 'fs::parser::ast::value_expression::value_expression(const fs::parser::ast::value_expression&)'  
     { ::new(static_cast<void*>(__p)) _T1(std::forward<_Args>(__args)...); }  
       ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
  
I think I have found the 'pattern' of ASTs that result in this error, trying to reproduce on a minimal example  
  
Copying: there have been previously multiple cases when X3 tried to copy AST types which should not happen (deep copying AST can be really expensive) and X3 should be usable with move-only types.

---

## Comment 3

> Username: Kojoley  
> Created at: 2019-02-17 23:05:15 UTC  
> Updated at: 2019-02-17 23:09:34 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-464523368  

What is the `fs::parser::ast::array_expression` type actually is? Please, show the definition.

---

## Comment 4

> Username: Xeverous  
> Created at: 2019-02-17 23:15:39 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-464524663  

MCVE:  
  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/home/x3/support/ast/variant.hpp>  
#include <vector>  
  
namespace x3 = boost::spirit::x3;  
  
namespace ast  
{  
	struct array_expression : std::vector<struct value_expression> {};  
  
	struct value_expression : x3::variant<array_expression, int>  
	{  
		using base_type::base_type;  
		using base_type::operator=;  
	};  
  
	struct grammar  
	{  
		grammar& operator=(value_expression ve)  
		{  
			value = std::move(ve);  
			return *this;  
		}  
  
		value_expression value;  
	};  
}  
  
x3::rule<class value_expression_class, ast::value_expression> value_expression;  
  
x3::rule<class array_expression_class, ast::array_expression> array_expression;  
auto const array_expression_def = '[' >> value_expression % ',' >> ']';  
BOOST_SPIRIT_DEFINE(array_expression)  
  
auto const value_expression_def = array_expression | x3::int_;  
BOOST_SPIRIT_DEFINE(value_expression)  
  
x3::rule<class grammar_class, ast::grammar> grammar;  
auto const grammar_def = value_expression;  
BOOST_SPIRIT_DEFINE(grammar)  
  
BOOST_SPIRIT_INSTANTIATE(decltype(grammar), char const*, x3::unused_type)  
```

---

## Comment 5

> Username: Kojoley  
> Created at: 2019-02-17 23:26:09 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-464525886  

It works for me (tried 1.69 and develop) https://wandbox.org/permlink/Yoyx6h3Abt6SAhSN

---

## Comment 6

> Username: Xeverous  
> Created at: 2019-02-17 23:31:31 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-464526533  

Change compiler to GCC

---

## Comment 7

> Username: djowel  
> Created at: 2019-02-17 23:59:03 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-464529784  

> Copying: there have been previously multiple cases when X3 tried to copy AST types which should not happen (deep copying AST can be really expensive) and X3 should be usable with move-only types.  
  
Could you point me to one example please? I'm trying to get on board.  
  
At any rate, how is copying relevant here?

---

## Comment 8

> Username: Kojoley  
> Created at: 2019-02-18 00:02:50 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-464530307  

It looks like a GCC bug for me. MSVC and Clang are ok. I replaced `x3::variant` with `std::variant` and the problem persists.  
  
```  
prog.cc:17:12: error: use of deleted function 'ast::value_expression::value_expression(const ast::value_expression&)'  
prog.cc:11:12: note: 'ast::value_expression::value_expression(const ast::value_expression&)' is implicitly declared as deleted because 'ast::value_expression' declares a move constructor or move assignment operator  
   11 |     struct value_expression : std::variant<array_expression, int>  
      |            ^~~~~~~~~~~~~~~~  
1  
```

---

## Comment 9

> Username: djowel  
> Created at: 2019-02-18 00:06:26 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-464530777  

@Kojoley , off topic, I assume std::variant also has the same "never empty guarantee" and inefficient move/copy issues? Have you tried?

---

## Comment 10

> Username: Kojoley  
> Created at: 2019-02-18 00:10:01 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-464531440  

The `std::variant` can be valueless i.e empty http://eel.is/c%2B%2Bdraft/variant#status.

---

## Comment 11

> Username: djowel  
> Created at: 2019-02-18 00:12:08 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-464531861  

> The `std::variant` can be valueless i.e empty http://eel.is/c%2B%2Bdraft/variant#status.  
  
We should probably add some benchmarks and start using std::variant if it gives good performance. I'm for retiring x3::variant if this is so.

---

## Comment 12

> Username: Kojoley  
> Created at: 2019-02-18 00:21:30 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-464533047  

> We should probably add some benchmarks and start using std::variant if it gives good performance.  
  
It does not have `recursive_wrapper` or any other simple way to have a recursive variant, we need to make something on Spirit side for this, for example adding native support for   
  
> I'm for retiring x3::variant if this is so.  
  
This also can happen with https://github.com/boostorg/variant/pull/59 :-)

---

## Comment 13

> Username: Kojoley  
> Created at: 2019-02-18 00:24:19 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-464533506  

Okay, this is definitively GCC bug. https://wandbox.org/permlink/t8ZFEJjq9F0JjHZt  
  
```cpp  
#include <vector>  
#include <variant>  
  
namespace ast  
{  
    struct array_expression : std::vector<struct value_expression> {};  
  
    struct value_expression : std::variant<array_expression, int>  
    {  
        using std::variant<array_expression, int>::variant;  
        using std::variant<array_expression, int>::operator=;  
    };  
  
    struct grammar  
    {  
        grammar& operator=(value_expression ve)  
        {  
            value = std::move(ve);  
            return *this;  
        }  
  
        value_expression value;  
    };  
}  
      
int main()  
{  
    ast::array_expression a;  
    std::vector<ast::value_expression> b;  
    a.insert(a.end(), b.begin(), b.end());  
}  
```  
  
If you remove `grammar` definition, it will compile. I am going to try to reduce the repro and will report to GCC folks.

---

## Comment 14

> Username: Kojoley  
> Created at: 2019-02-18 00:51:48 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-464537240  

Found a workaround, place something that will instantiate the `value_expression` copy constructor before `grammar` definition, like `using workaround = decltype(value_expression{*static_cast<value_expression const*>(0)});` https://wandbox.org/permlink/SznOGzBiX1Wlx5M1

---

## Comment 15

> Username: Kojoley  
> Created at: 2019-02-18 01:05:17 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-464539147  

Created a bug report https://gcc.gnu.org/bugzilla/show_bug.cgi?id=89381

---

## Comment 16

> Username: cppljevans  
> Created at: 2019-02-18 04:28:32 UTC  
> Updated at: 2019-02-27 19:45:43 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-464579535  

On 2/17/19 5:26 PM, Nikita Kniazev wrote:  
> It works for me (tried 1.69 and develop) https://wandbox.org/permlink/Yoyx6h3Abt6SAhSN  
>   
I compiled with empty main, but when put phrase_parse, clang also failed   
to parse.  Did I do something wrong?  
  
https://wandbox.org/permlink/94chZtX6p8b7w5LS  
  
The compile error messages started with:  
  
```bash  
/opt/wandbox/boost-1.69.0/clang-7.0.0/include/boost/spirit/home/x3/support/traits/move_to.hpp:179:9:   
note: candidate function template not viable: requires 4 arguments, but   
3 were provided  
         move_to(Iterator first, Iterator last, Dest& dest, tuple_attribute)  
         ^  
/opt/wandbox/boost-1.69.0/clang-7.0.0/include/boost/spirit/home/x3/support/traits/move_to.hpp:186:9:   
note: candidate function template not viable: requires 4 arguments, but   
3 were provided  
         move_to(Iterator first, Iterator last,   
boost::iterator_range<Iterator>& rng, range_attribute)  
         ^  
```  
  
I tried to copy whatever you had on your wandbox, but maybe I missed   
something?

---

## Comment 17

> Username: cppljevans  
> Created at: 2019-02-18 04:38:23 UTC  
> Updated at: 2019-02-27 19:45:47 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-464581280  

On 2/17/19 10:28 PM, cppljevans wrote:  
> On 2/17/19 5:26 PM, Nikita Kniazev wrote:  
>> It works for me (tried 1.69 and develop) https://wandbox.org/permlink/Yoyx6h3Abt6SAhSN  
>>  
> I compiled with empty main, but when put phrase_parse, clang also failed  
> to parse.  Did I do something wrong?  
>   
> https://wandbox.org/permlink/94chZtX6p8b7w5LS  
>   
> The compile error messages started with:  
>   
> ```bash  
> /opt/wandbox/boost-1.69.0/clang-7.0.0/include/boost/spirit/home/x3/support/traits/move_to.hpp:179:9:  
> note: candidate function template not viable: requires 4 arguments, but  
> 3 were provided  
>           move_to(Iterator first, Iterator last, Dest& dest, tuple_attribute)  
>           ^  
> /opt/wandbox/boost-1.69.0/clang-7.0.0/include/boost/spirit/home/x3/support/traits/move_to.hpp:186:9:  
> note: candidate function template not viable: requires 4 arguments, but  
> 3 were provided  
>           move_to(Iterator first, Iterator last,  
> boost::iterator_range<Iterator>& rng, range_attribute)  
>           ^  
> ```  
>   
> I tried to copy whatever you had on your wandbox, but maybe I missed  
> something?  
>   
>   
OOPS.  Wrong attribute type.  Should have been ast::grammar.  
Sorry for noise.

---

## Comment 18

> Username: Xeverous  
> Created at: 2019-02-18 10:00:52 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-464666389  

> Could you point me to one example please? I'm trying to get on board.  
  
https://github.com/boostorg/spirit/issues/444  
  
> At any rate, how is copying relevant here?  
  
It causes a compilation error (I have some move-only types in AST). This issue seems to be a compiler bug instead of previously expected X3 bug.

---

## Comment 19

> Username: Xeverous  
> Created at: 2019-02-18 10:32:04 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-464677392  

@Kojoley We have found a GCC bug but isn't there also an X3 bug? If you add:  
  
```cpp  
    value_expression(const value_expression&) = delete;  
    value_expression& operator=(value_expression) = delete;  
```  
  
you get a compiler bug due to use of deleted copy ctor. In both Clang and workarounded GCC ast is copied.

---

## Comment 20

> Username: djowel  
> Created at: 2019-02-18 13:37:05 UTC  
> Updated at: 2019-02-18 13:39:31 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-464733983  

> > Could you point me to one example please? I'm trying to get on board.  
>   
> boostorg/spirit#444  
>   
> > At any rate, how is copying relevant here?  
>   
> It causes a compilation error (I have some move-only types in AST). This issue seems to be a compiler bug instead of previously expected X3 bug.  
  
Indeed I conceptualized X3 to take advantage of move semantics. That said, I am not sure if we can allow fully move-only attributes, and not being in the docs (as you said: "It's also nowhere stated in the documentation that types need to be copyable.") does not imply the opposite (that move-only attributes are allowed).   
  
So I do not consider it a **bug**, per-se, although I agree at the very least that copying should be minimized as much as possible for the sake of efficiency.

---

## Comment 21

> Username: Kojoley  
> Created at: 2019-02-18 14:17:23 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-464746960  

> isn't there also an X3 bug?  
  
Yes, it is a performance bug, but actually it is a part more general problem with backtracking boostorg/spirit#835 (excessive backtracking where it is not needed, and no backtracking where it is needed).

---

## Comment 22

> Username: djowel  
> Created at: 2019-02-18 14:22:54 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-464748800  

> > isn't there also an X3 bug?  
>   
> Yes, it is a performance bug, but actually it is a part more general problem with backtracking boostorg/spirit#835 (excessive backtracking where it is not needed, and no backtracking where it is needed).  
  
Again it is not a "bug". So please stop characterizing it as such. Move optimization is ideal, but not strictly required.

---

## Comment 23

> Username: djowel  
> Created at: 2019-02-18 14:24:46 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-464749419  

> boostorg/spirit#835 (excessive backtracking where it is not needed, and no backtracking where it is needed).  
  
I'll go study your proposal in earnest and maybe we can come up with a better move optimization scheme.

---

## Comment 24

> Username: Kojoley  
> Created at: 2019-02-18 14:45:37 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-464757190  

> I'll go study your proposal in earnest and maybe we can come up with a better move optimization scheme.  
  
Actually I am not remember myself its details. I have a rewrite of alternate and sequence parsers in mind (flatten instead of a binary tree), with it should make easier to implement efficient backtracking.  
  
As for now we can replace copying with moving if not in all but in most places surely, I will try to make a PR.

---

## Comment 25

> Username: djowel  
> Created at: 2019-02-18 14:53:23 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-464760242  

> As for now we can replace copying with moving if not in all but in most places surely, I will try to make a PR.  
  
One more thing we can do is augment the tests with move-only attributes so we can better know what can be improved now.

---

## Comment 26

> Username: cppljevans  
> Created at: 2019-02-18 15:00:06 UTC  
> Updated at: 2019-02-18 15:12:57 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-464763219  

> > I'll go study your proposal in earnest and maybe we can come up with a better move optimization scheme.  
>   
> Actually I am not remember myself its details. I have a rewrite of alternate and sequence parsers in mind (flatten instead of a binary tree), with it should make easier to implement efficient backtracking.  
>   
  
I like flattening idea!  
  
I should probably say why.  By flattening, the parser, I assume, would have a package  
of parsers instead of just Left and Right.  This would make it "structurally" more  
like the attributes.  I assume that the increased similarity would make it easier  
to understand the attribute and parser interactions; hence, make it easier to see   
where errors might occur and less likely to make errors during maintenance.  
  
Hope that's clear.

---

## Comment 27

> Username: djowel  
> Created at: 2019-02-18 15:14:20 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-464769105  

> I have a rewrite of alternate and sequence parsers in mind (flatten instead of a binary tree), with it should make easier to implement efficient backtracking.  
  
I'm curious how you can do that without flattening the expression tree (like it was done in Qi), which leads to slower compile time. Hmmm... but thinking about it now, it might be possible to have a flattened expression tree view.

---

## Comment 28

> Username: cppljevans  
> Created at: 2019-02-18 15:19:50 UTC  
> Updated at: 2019-02-18 15:42:58 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-464771421  

> > I have a rewrite of alternate and sequence parsers in mind (flatten instead of a binary tree), with it should make easier to implement efficient backtracking.  
>   
> I'm curious how you can do that without flattening the expression tree (like it was done in Qi), which leads to slower compile time. Hmmm... but thinking about it now, it might be possible to have a flattened expression tree view.  
  
Yep, I thought about that too, and I don't know if it would increase compile times.  I was hoping that   
the same template metaprogramming which flattens the alternative or sequence attributes  
could just be modified to flatten the parsers also, but that's just a guess.  I actually  
had some code that did flatten the parsers, but never did any benchmarks and  
I don't recall even using attributes.  It was a prototype.

---

## Comment 29

> Username: Kojoley  
> Created at: 2019-02-18 15:24:26 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-464773326  

> I'm curious how you can do that without flattening the expression tree (like it was done in Qi), which leads to slower compile time.  
  
I am sure that flattening the expression tree itself is fast, the slow thing is recursion in fusion::for_each/any and spirit::any_if, with variadic templates we do not it.

---

## Comment 30

> Username: djowel  
> Created at: 2019-02-18 15:29:51 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-464775542  

> I am sure that flattening the expression tree itself is fast, the slow thing is recursion in fusion::for_each/any and spirit::any_if, with variadic templates we do not it.  
  
Yes. I'd do it without fusion; only variadic templates. I'd probably also do it using lazy flattened-views. In any case, I think there will be some overhead, so we need some compile time tests to make sure we're not sacrificing too much.

---

## Comment 31

> Username: Xeverous  
> Created at: 2019-02-27 19:29:10 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-467997661  

FYI I still get compilation issues after boostorg/spirit#472 although now they are different (doesn't look like GCC bug) (now template backtrace points to `alternative`). Minimal example from above does compile, so I will try to reproduce again.

---

## Comment 32

> Username: Kojoley  
> Created at: 2019-02-27 20:01:53 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-468009008  

Interesting, I cannot even imagine where copying could happen.

---

## Comment 33

> Username: Xeverous  
> Created at: 2019-02-27 20:08:06 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-468011102  

New MCVE:  
  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <vector>  
  
namespace x3 = boost::spirit::x3;  
  
namespace ast  
{  
	struct value_expression  
	{  
		value_expression& operator=(int n)  
		{  
			value = n;  
			return *this;  
		}  
  
		value_expression(const value_expression&) = delete;  
		value_expression& operator=(const value_expression&) = delete;  
		value_expression(value_expression&&) = default;  
		value_expression& operator=(value_expression&&) = default;  
  
		int value;  
	};  
  
	struct grammar  
	{  
		grammar& operator=(value_expression ve)  
		{  
			value = std::move(ve);  
			return *this;  
		}  
  
		value_expression value;  
	};  
}  
  
x3::rule<class value_expression_class, ast::value_expression> value_expression;  
auto const value_expression_def = x3::int_;  
BOOST_SPIRIT_DEFINE(value_expression)  
  
x3::rule<class grammar_class, ast::grammar> grammar;  
auto const grammar_def = ((value_expression % ",") | x3::attr(std::vector<ast::value_expression>()));  
BOOST_SPIRIT_DEFINE(grammar)  
  
BOOST_SPIRIT_INSTANTIATE(decltype(grammar), char const*, x3::unused_type)  
```

---

## Comment 34

> Username: Kojoley  
> Created at: 2019-02-27 20:27:14 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-468017340  

That's because `attr` can be used only with copyable objects (and vector of uncopyable things cannot be copied even if it is empty), it needs to give you something every time you hit it. I have made a custom parser for tests exactly because of this https://github.com/boostorg/spirit/blob/32537028f5aad270db4df86e4e82b5ffa7d42027/test/x3/utils.hpp#L20-L43

---

## Comment 35

> Username: Xeverous  
> Created at: 2019-02-27 20:50:50 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-468025002  

Ok, copying indeed makes sense in this situation. Fortunately the newly discovered bug does not affect me because I always supply empty containers to `attr`.

---

## Comment 36

> Username: Xeverous  
> Created at: 2019-10-17 22:41:16 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-543391188  

@Kojoley could you write a reply on the linked GCC bugzilla? The bug has been "fixed" in 9.0 but it has actually turned into an unwated warning.  
  
Comparison: https://godbolt.org/z/WgIH4c

---

## Comment 37

> Username: Kojoley  
> Created at: 2019-10-17 23:33:06 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-543407880  

The `-Wdeprecated-copy` diagnostic was added in GCC 9. I am not sure if it is wrong here, but it is indeed uninformative and annoying in the particular case. I have opened a new ticket https://gcc.gnu.org/bugzilla/show_bug.cgi?id=92145.

---

## Comment 38

> Username: cppljevans  
> Created at: 2023-05-08 04:27:40 UTC  
> Url: https://github.com/boostorg/spirit/issues/465#issuecomment-1537729045  

> > I'll go study your proposal in earnest and maybe we can come up with a better move optimization scheme.  
>   
> Actually I am not remember myself its details. I have a rewrite of alternate and sequence parsers in mind (flatten instead of a binary tree), with it should make easier to implement efficient backtracking.  
>   
> As for now we can replace copying with moving if not in all but in most places surely, I will try to make a PR.  
  
A prototype of this flattening (termed unfold'ing) was mentioned [here](https://github.com/eido79/spirit/commit/7e62f9005bffb19733d6fafee8380ee486ee1337#commitcomment-112314692).
