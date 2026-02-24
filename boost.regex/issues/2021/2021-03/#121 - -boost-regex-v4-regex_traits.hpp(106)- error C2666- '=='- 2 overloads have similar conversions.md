# #121 - [std:c++latest][MSVC] \boost/regex/v4/regex_traits.hpp(106): error C2666: '==': 2 overloads have similar conversions [Closed]

> Username: QuellaZhang  
> Created at: 2021-03-04 10:07:17 UTC  
> Updated at: 2021-03-06 20:02:12 UTC  
> Closed at: 2021-03-06 20:02:12 UTC  
> Url: https://github.com/boostorg/regex/issues/121  

Issue description:  
Boost\libs\regex failed to build due to error C2666  with /std:c++latest in MSVC. Could you please take a look?  
  
Reproduce steps:  
  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
2. open a VS 2019 x64 command prompt and browse to D:\Boost\src  
3. set _CL_=/std:c++latest  
4. .\bootstrap  
5. .\b2 headers variant=release --build-dir=..\out\x64rel address-model=64  
6. .\b2 variant=release --build-dir=..\out\x64rel address-model=64  
7. .\b2 -j4 variant=release --build-dir=..\out\x64rel libs\regex\test  
  
build log: [test.log.75.log](https://github.com/boostorg/regex/files/6082618/test.log.75.log)  
  
ErrorMessage:  
\boost/regex/v4/regex_traits.hpp(106): error C2666: '==': 2 overloads have similar conversions  
.\boost/regex/concepts.hpp(57): note: could be 'bool boost::char_architype::operator ==(const boost::char_architype &) const' [synthesized expression 'y == x']  
.\boost/regex/v4/regex_traits.hpp(106): note: or       'built-in C++ operator==(long, long)'  
.\boost/regex/v4/regex_traits.hpp(106): note: while trying to match the argument list '(long, boost::char_architype)'  
.\boost/regex/v4/regex_traits.hpp(105): note: while compiling class template member function 'boost::regex_constants::syntax_type boost::re_detail_107600::default_wrapper<BaseT>::syntax_type(boost::char_architype) const'  
        with  
        [  
            BaseT=boost::regex_traits_architype<boost::char_architype>  
        ]  
.\boost/regex/v4/basic_regex_parser.hpp(277): note: see reference to function template instantiation 'boost::regex_constants::syntax_type boost::re_detail_107600::default_wrapper<BaseT>::syntax_type(boost::char_architype) const' being compiled  
        with  
        [  
            BaseT=boost::regex_traits_architype<boost::char_architype>  
        ]  
.\boost/regex/v4/regex_traits.hpp(168): note: see reference to class template instantiation 'boost::re_detail_107600::default_wrapper<BaseT>' being compiled  
        with  
        [  
            BaseT=boost::regex_traits_architype<boost::char_architype>  
        ]  
.\boost/regex/v4/basic_regex.hpp(242): note: see reference to class template instantiation 'boost::regex_traits_wrapper<traits>' being compiled  
        with  
        [  
            traits=boost::regex_traits_architype<boost::char_architype>  
        ]  
.\boost/regex/v4/basic_regex.hpp(241): note: while compiling class template member function 'boost::copy_constructible_archetype<boost::assignable_archetype<boost::null_archetype<int>>> boost::re_detail_107600::basic_regex_implementation<charT,traits>::getloc(void) const'  
        with  
        [  
            charT=boost::char_architype,  
            traits=boost::regex_traits_architype<boost::char_architype>  
        ]

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-03-06 20:02:12 UTC  
> Url: https://github.com/boostorg/regex/issues/121#issuecomment-792043306  

OK, that's a test case that fails rather than building/using the library.  
  
In any case Regex has been substantially reworked for the next release, and this issue is no longer present (though I have spotted an unrelated one which I'll investigate shortly).
