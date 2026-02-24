# #187 - boost::fusion::for_each is broken [Closed]

> Username: apolukhin  
> Created at: 2018-07-09 21:19:41 UTC  
> Updated at: 2018-11-02 16:04:21 UTC  
> Closed at: 2018-07-10 06:04:56 UTC  
> Url: https://github.com/boostorg/fusion/issues/187  

Previously this code was compiling: https://github.com/apolukhin/Boost-Cookbook/blob/second_edition/Chapter08/06_tuple_to_string/main.cpp#L1-L26  
  
But now it produces error messages:  
```  
main.cpp:24:28: error: use of deleted function ‘stringize_functor::stringize_functor(stringize_functor&&)’  
     boost::fusion::for_each(seq, stringize_functor(result));  
     ~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
main.cpp:4:8: note: ‘stringize_functor::stringize_functor(stringize_functor&&)’ is implicitly deleted because the default definition would be ill-formed:  
 struct stringize_functor: boost::noncopyable {  
        ^~~~~~~~~~~~~~~~~  
main.cpp:4:8: error: use of deleted function ‘boost::noncopyable_::noncopyable::noncopyable(const boost::noncopyable_::noncopyable&)’  
In file included from /home/travis/boost-local/boost/utility.hpp:22:0,  
                 from /home/travis/boost-local/boost/range/size.hpp:25,  
                 from /home/travis/boost-local/boost/range/functions.hpp:20,  
                 from /home/travis/boost-local/boost/range/iterator_range_core.hpp:38,  
                 from /home/travis/boost-local/boost/lexical_cast.hpp:30,  
                 from main.cpp:1:  
/home/travis/boost-local/boost/core/noncopyable.hpp:34:7: note: declared here  
       noncopyable( const noncopyable& ) = delete;  
       ^~~~~~~~~~~  
In file included from /home/travis/boost-local/boost/fusion/include/for_each.hpp:11:0,  
                 from main.cpp:19:  
/home/travis/boost-local/boost/fusion/algorithm/iteration/for_each.hpp:41:5: note:   initializing argument 2 of ‘constexpr typename boost::enable_if<boost::fusion::traits::is_sequence<Sequence> >::type boost::fusion::for_each(const Sequence&, F) [with Sequence = boost::fusion::vector<cat, int, std::basic_string<char, std::char_traits<char>, std::allocator<char> > >; F = stringize_functor; typename boost::enable_if<boost::fusion::traits::is_sequence<Sequence> >::type = void]’  
     for_each(Sequence const& seq, F f)  
     ^~~~~~~~  
main.cpp: In instantiation of ‘std::string stringize(const Sequence&) [with Sequence = boost::tuples::tuple<cat, int, std::basic_string<char> >; std::string = std::basic_string<char>]’:  
main.cpp:55:26:   required from here  
main.cpp:24:28: error: use of deleted function ‘stringize_functor::stringize_functor(stringize_functor&&)’  
     boost::fusion::for_each(seq, stringize_functor(result));  
     ~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /home/travis/boost-local/boost/fusion/include/for_each.hpp:11:0,  
                 from main.cpp:19:  
/home/travis/boost-local/boost/fusion/algorithm/iteration/for_each.hpp:41:5: note:   initializing argument 2 of ‘constexpr typename boost::enable_if<boost::fusion::traits::is_sequence<Sequence> >::type boost::fusion::for_each(const Sequence&, F) [with Sequence = boost::tuples::tuple<cat, int, std::basic_string<char> >; F = stringize_functor; typename boost::enable_if<boost::fusion::traits::is_sequence<Sequence> >::type = void]’  
     for_each(Sequence const& seq, F f)  
     ^~~~~~~~  
main.cpp: In instantiation of ‘std::string stringize(const Sequence&) [with Sequence = std::pair<cat, cat>; std::string = std::basic_string<char>]’:  
main.cpp:56:26:   required from here  
main.cpp:24:28: error: use of deleted function ‘stringize_functor::stringize_functor(stringize_functor&&)’  
     boost::fusion::for_each(seq, stringize_functor(result));  
     ~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /home/travis/boost-local/boost/fusion/include/for_each.hpp:11:0,  
                 from main.cpp:19:  
/home/travis/boost-local/boost/fusion/algorithm/iteration/for_each.hpp:41:5: note:   initializing argument 2 of ‘constexpr typename boost::enable_if<boost::fusion::traits::is_sequence<Sequence> >::type boost::fusion::for_each(const Sequence&, F) [with Sequence = std::pair<cat, cat>; F = stringize_functor; typename boost::enable_if<boost::fusion::traits::is_sequence<Sequence> >::type = void]’  
     for_each(Sequence const& seq, F f)  
     ^~~~~~~~  
main.cpp: In instantiation of ‘std::string stringize(const Sequence&) [with Sequence = boost::array<cat, 10ul>; std::string = std::basic_string<char>]’:  
main.cpp:57:31:   required from here  
main.cpp:24:28: error: use of deleted function ‘stringize_functor::stringize_functor(stringize_functor&&)’  
     boost::fusion::for_each(seq, stringize_functor(result));  
     ~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /home/travis/boost-local/boost/fusion/include/for_each.hpp:11:0,  
                 from main.cpp:19:  
/home/travis/boost-local/boost/fusion/algorithm/iteration/for_each.hpp:41:5: note:   initializing argument 2 of ‘constexpr typename boost::enable_if<boost::fusion::traits::is_sequence<Sequence> >::type boost::fusion::for_each(const Sequence&, F) [with Sequence = boost::array<cat, 10ul>; F = stringize_functor; typename boost::enable_if<boost::fusion::traits::is_sequence<Sequence> >::type = void]’  
     for_each(Sequence const& seq, F f)  
     ^~~~~~~~  
make[2]: *** [main.o] Error 1  
```  
  
The breaking change probably was introduced in commit https://github.com/boostorg/fusion/commit/102588ae4636ffb418ee678d5d71d110bfcd0eb4

---

## Comment 1

> Username: Flast  
> Created at: 2018-07-10 05:28:29 UTC  
> Url: https://github.com/boostorg/fusion/issues/187#issuecomment-403704759  

The implementation was not correct for a long time; documentation was correct. `for_each` should take functor by value not reference.  
So, if you need pass non-copyable functor, `std::ref` will help.

---

## Comment 2

> Username: apolukhin  
> Created at: 2018-07-10 06:04:53 UTC  
> Url: https://github.com/boostorg/fusion/issues/187#issuecomment-403710621  

OK. Thanks for the clarification

---

## Comment 3

> Username: Kojoley  
> Created at: 2018-11-02 16:04:21 UTC  
> Url: https://github.com/boostorg/fusion/issues/187#issuecomment-435429247  

The problem is that `std::ref` cannot be used in C++98 code and `boost::ref` does not have `operator()`.
