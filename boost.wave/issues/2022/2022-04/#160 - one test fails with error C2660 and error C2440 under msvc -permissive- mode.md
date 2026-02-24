# #160 - one test fails with error C2660 and error C2440 under msvc /permissive- mode [Closed]

> Username: Zhaojun-Liu  
> Created at: 2022-04-12 10:16:34 UTC  
> Updated at: 2022-06-23 01:49:21 UTC  
> Closed at: 2022-06-23 00:36:20 UTC  
> Url: https://github.com/boostorg/wave/issues/160  

**Issue description:**  
One test fails after runtest in MSVC. Could you please take a look?  
  
**Reproduce steps:**  
1. git clone ​https://github.com/boostorg/boost.git F:\gitP\boostorg\boost  
2. git -C "F:\gitP\boostorg\boost" submodule update --init --recursive  
3. open a VS 2019 x86 command prompt and go to F:\gitP\boostorg\boost  
4. set _CL_=/permissive-  
5. .\b2 -j16 variant=release --build-dir=..\out\x86rel libs\wave\test  
  
[test_95.log](https://github.com/boostorg/wave/files/8471946/test_95.log)  
  
**ErrorMessage:**  
```  
test_lexertl_lexer.cpp  
F:\gitP\boostorg\boost\libs/wave/samples/list_includes/lexertl/lexertl_lexer.hpp(432): error C2660: 'boost::wave::operator |': function does not take 1 arguments  
F:\gitP\boostorg\boost\boost/wave/token_ids.hpp(341): note: see declaration of 'boost::wave::operator |'  
F:\gitP\boostorg\boost\libs/wave/samples/list_includes/lexertl/lexertl_lexer.hpp(430): note: while compiling class template static data member 'const boost::wave::cpplexer::lexertl::lexer::lexertl<boost::wave::util::position_iterator<Iterator,Position>,Position>::lexer_data boost::wave::cpplexer::lexertl::lexer::lexertl<boost::wave::util::position_iterator<Iterator,Position>,Position>::init_data_cpp0x[15]'  
        with  
        [  
            Iterator=char *,  
            Position=boost::wave::util::file_position<boost::wave::util::flex_string<char,std::char_traits<char>,std::allocator<char>,boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char,std::allocator<char>>,char *>>>  
        ]  
F:\gitP\boostorg\boost\libs/wave/samples/list_includes/lexertl/lexertl_lexer.hpp(825): note: while compiling class template static data member 'boost::wave::cpplexer::lexertl::lexer::lexertl<boost::wave::util::position_iterator<Iterator,Position>,Position> boost::wave::cpplexer::lexertl::lexertl_functor<Iterator,Position>::lexer_'  
        with  
        [  
            Iterator=char *,  
            Position=boost::wave::util::file_position<boost::wave::util::flex_string<char,std::char_traits<char>,std::allocator<char>,boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char,std::allocator<char>>,char *>>>  
        ]  
F:\gitP\boostorg\boost\libs/wave/samples/list_includes/lexertl/lexertl_lexer.hpp(882): note: see reference to class template instantiation 'boost::wave::cpplexer::lexertl::lexertl_functor<Iterator,Position>' being compiled  
        with  
        [  
            Iterator=char *,  
            Position=boost::wave::util::file_position<boost::wave::util::flex_string<char,std::char_traits<char>,std::allocator<char>,boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char,std::allocator<char>>,char *>>>  
        ]  
F:\gitP\boostorg\boost\libs/wave/samples/list_includes/lexertl/lexertl_lexer.hpp(881): note: while compiling class template member function 'boost::wave::cpplexer::lex_input_interface<TokenT> *boost::wave::cpplexer::lexertl::new_lexer_gen<IteratorT,boost::wave::util::file_position<boost::wave::util::flex_string<char,std::char_traits<char>,std::allocator<char>,boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char,std::allocator<char>>,char *>>>>::new_lexer(const IteratorT &,const IteratorT &,const PositionT &,boost::wave::language_support)'  
        with  
        [  
            TokenT=token_type,  
            IteratorT=char *,  
            PositionT=boost::wave::util::file_position<boost::wave::util::flex_string<char,std::char_traits<char>,std::allocator<char>,boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char,std::allocator<char>>,char *>>>  
        ]  
F:\gitP\boostorg\boost\libs\wave\samples\list_includes\lexertl_interface.hpp(69): note: see reference to function template instantiation 'boost::wave::cpplexer::lex_input_interface<TokenT> *boost::wave::cpplexer::lexertl::new_lexer_gen<IteratorT,boost::wave::util::file_position<boost::wave::util::flex_string<char,std::char_traits<char>,std::allocator<char>,boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char,std::allocator<char>>,char *>>>>::new_lexer(const IteratorT &,const IteratorT &,const PositionT &,boost::wave::language_support)' being compiled  
        with  
        [  
            TokenT=token_type,  
            IteratorT=char *,  
            PositionT=boost::wave::util::file_position<boost::wave::util::flex_string<char,std::char_traits<char>,std::allocator<char>,boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char,std::allocator<char>>,char *>>>  
        ]  
F:\gitP\boostorg\boost\libs\wave\samples\list_includes\lexertl_interface.hpp(69): note: see reference to class template instantiation 'boost::wave::cpplexer::lexertl::new_lexer_gen<IteratorT,boost::wave::util::file_position<boost::wave::util::flex_string<char,std::char_traits<char>,std::allocator<char>,boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char,std::allocator<char>>,char *>>>>' being compiled  
        with  
        [  
            IteratorT=char *  
        ]  
F:\gitP\boostorg\boost\libs\wave\samples\list_includes\lexertl\../lexertl_iterator.hpp(185): note: see reference to function template instantiation 'boost::wave::cpplexer::lex_input_interface<TokenT> *boost::wave::cpplexer::lexertl::lexertl_input_interface<TokenT>::new_lexer<IteratorT>(const IteratorT &,const IteratorT &,const boost::wave::util::file_position<boost::wave::util::flex_string<char,std::char_traits<char>,std::allocator<char>,boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char,std::allocator<char>>,char *>>> &,boost::wave::language_support)' being compiled  
        with  
        [  
            TokenT=token_type,  
            IteratorT=char *  
        ]  
F:\gitP\boostorg\boost\libs\wave\samples\list_includes\lexertl\../lexertl_iterator.hpp(181): note: see reference to function template instantiation 'boost::wave::cpplexer::lex_input_interface<TokenT> *boost::wave::cpplexer::lexertl::lexertl_input_interface<TokenT>::new_lexer<IteratorT>(const IteratorT &,const IteratorT &,const boost::wave::util::file_position<boost::wave::util::flex_string<char,std::char_traits<char>,std::allocator<char>,boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char,std::allocator<char>>,char *>>> &,boost::wave::language_support)' being compiled  
        with  
        [  
            TokenT=token_type,  
            IteratorT=char *  
        ]  
libs\wave\test\build\..\testlexers\test_lexertl_lexer.cpp(53): note: see reference to function template instantiation 'boost::wave::cpplexer::lexertl::lex_iterator<token_type>::lex_iterator<char*>(const IteratorT &,const IteratorT &,const boost::wave::util::file_position<boost::wave::util::flex_string<char,std::char_traits<char>,std::allocator<char>,boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char,std::allocator<char>>,char *>>> &,boost::wave::language_support)' being compiled  
        with  
        [  
            IteratorT=char *  
        ]  
libs\wave\test\build\..\testlexers\test_lexertl_lexer.cpp(52): note: see reference to function template instantiation 'boost::wave::cpplexer::lexertl::lex_iterator<token_type>::lex_iterator<char*>(const IteratorT &,const IteratorT &,const boost::wave::util::file_position<boost::wave::util::flex_string<char,std::char_traits<char>,std::allocator<char>,boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char,std::allocator<char>>,char *>>> &,boost::wave::language_support)' being compiled  
        with  
        [  
            IteratorT=char *  
        ]  
F:\gitP\boostorg\boost\libs/wave/samples/list_includes/lexertl/lexertl_lexer.hpp(434): error C2660: 'boost::wave::operator |': function does not take 1 arguments  
F:\gitP\boostorg\boost\boost/wave/token_ids.hpp(341): note: see declaration of 'boost::wave::operator |'  
F:\gitP\boostorg\boost\libs/wave/samples/list_includes/lexertl/lexertl_lexer.hpp(438): error C2660: 'boost::wave::operator |': function does not take 1 arguments  
F:\gitP\boostorg\boost\boost/wave/token_ids.hpp(341): note: see declaration of 'boost::wave::operator |'  
F:\gitP\boostorg\boost\libs/wave/samples/list_includes/lexertl/lexertl_lexer.hpp(432): error C2440: 'initializing': cannot convert from 'const char [58]' to 'boost::wave::token_id'  
F:\gitP\boostorg\boost\libs/wave/samples/list_includes/lexertl/lexertl_lexer.hpp(432): note: There is no context in which this conversion is possible  
F:\gitP\boostorg\boost\libs/wave/samples/list_includes/lexertl/lexertl_lexer.hpp(434): error C2440: 'initializing': cannot convert from 'const char [60]' to 'boost::wave::token_id'  
F:\gitP\boostorg\boost\libs/wave/samples/list_includes/lexertl/lexertl_lexer.hpp(434): note: There is no context in which this conversion is possible  
F:\gitP\boostorg\boost\libs/wave/samples/list_includes/lexertl/lexertl_lexer.hpp(438): error C2440: 'initializing': cannot convert from 'const char [57]' to 'boost::wave::token_id'  
F:\gitP\boostorg\boost\libs/wave/samples/list_includes/lexertl/lexertl_lexer.hpp(438): note: There is no context in which this conversion is possible  
```

---

## Comment 1

> Username: jefftrull  
> Created at: 2022-04-12 21:22:34 UTC  
> Url: https://github.com/boostorg/wave/issues/160#issuecomment-1097232890  

Thank you for the excellent bug report. It may some time before we get to it, unfortunately.

---

## Comment 2

> Username: jefftrull  
> Created at: 2022-06-22 18:42:08 UTC  
> Url: https://github.com/boostorg/wave/issues/160#issuecomment-1163480177  

I've reproduced this, but I'm not sure yet what is happening and particularly why `/permissive-` makes a difference. Investigating.

---

## Comment 3

> Username: jefftrull  
> Created at: 2022-06-22 21:31:05 UTC  
> Url: https://github.com/boostorg/wave/issues/160#issuecomment-1163625754  

Disabling "two-phase conformance" (a new MSVC feature) with `/Zc:twoPhase-` makes this work again. Still investigating.

---

## Comment 4

> Username: jefftrull  
> Created at: 2022-06-23 00:36:20 UTC  
> Url: https://github.com/boostorg/wave/issues/160#issuecomment-1163800987  

The develop branch now contains a fix for this issue. Please reopen if you encounter any problems.

---

## Comment 5

> Username: Zhaojun-Liu  
> Created at: 2022-06-23 01:49:20 UTC  
> Url: https://github.com/boostorg/wave/issues/160#issuecomment-1163834178  

Ok. Thank you, I'll verify this issue in a follow-up test.
