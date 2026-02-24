# #80 - Warnings due to uninitialized members when Code Analysis is enabled in VS 2017 [Closed]

> Username: jlepola  
> Created at: 2019-03-07 14:42:48 UTC  
> Updated at: 2020-01-22 13:35:04 UTC  
> Closed at: 2020-01-22 13:35:04 UTC  
> Url: https://github.com/boostorg/regex/issues/80  

I get the following warnings if I set the Code Analysis level to "Microsoft Native Recommended Rules" in my project that is using boost/regex.    
  
Would it be reasonable to just initialize the members even though they will be reset in an initialization method called by the constructor? Alternatively those warnings can be disabled with a #pragma,  
  
Attached a sample project.  
[BoostRegex.zip](https://github.com/boostorg/regex/files/2941742/BoostRegex.zip)  
  
Warnings:  
`  
c:\git\boost\boost\regex\v4\cpp_regex_traits.hpp(362): warning C26495: Variable 'boost::re_detail_106900::cpp_regex_traits_char_layer<char>::m_char_map' is uninitialized. Always initialize a member variable (type.6).  
c:\git\boost\boost\regex\v4\cpp_regex_traits.hpp(367): warning C26495: Variable 'boost::re_detail_106900::cpp_regex_traits_char_layer<char>::m_char_map' is uninitialized. Always initialize a member variable (type.6).  
c:\git\boost\boost\regex\v4\w32_regex_traits.hpp(260): warning C26495: Variable 'boost::re_detail_106900::w32_regex_traits_char_layer<char>::m_char_map' is uninitialized. Always initialize a member variable (type.6).  
c:\git\boost\boost\regex\v4\w32_regex_traits.hpp(133): warning C26495: Variable 'boost::re_detail_106900::w32_regex_traits_base<char>::m_locale' is uninitialized. Always initialize a member variable (type.6).  
c:\git\boost\boost\regex\v4\cpp_regex_traits.hpp(176): warning C26495: Variable 'boost::re_detail_106900::cpp_regex_traits_base<char>::m_pctype' is uninitialized. Always initialize a member variable (type.6).  
`
