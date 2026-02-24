# #265 - Strange "direct access" warning on OSX for basic_text_oprimitive [Closed]

> Username: mcraveiro  
> Created at: 2022-09-29 08:16:20 UTC  
> Updated at: 2023-11-21 21:53:46 UTC  
> Closed at: 2023-11-21 21:53:46 UTC  
> Url: https://github.com/boostorg/serialization/issues/265  

**Please Note:** This query was originally submitted as an email to boost-users [0] before I saw Issues in GitHub project.  
  
Hi boost-serialization developers,  
  
First, a great thanks to the authors of the boost serialisation library, which I have used successfully across projects and has proven invaluable. Now to my problem. I have been investigating for a little while a strange warning I get when linking on OSX; the results of my investigation can be found here [1]. The TL; DR of it is as follows: my OSX debug build has a warning (indented for ease of reading):  
  
```  
    ld: warning: direct access in function  
    'boost::archive::basic_text_oprimitive<  
        std::__1::basic_ostream<char, std::__1::char_traits<char> >  
     >::~basic_text_oprimitive()'  
  
    from file  
  
    'vcpkg_installed/x64-osx/debug/lib/libboost_serialization.a(basic_text_oprimitive.o)'  
  
    to global weak symbol  
  
    'std::__1::basic_ostream<char, std::__1::char_traits<char> >&  
     std::__1::endl<char, std::__1::char_traits<char> >  
     (std::__1::basic_ostream<char, std::__1::char_traits<char> >&)'  
```  
  
This warning is caused by a mismatch in visibility settings between my project and vcpkg's build of boost. However, as far as I can see, both projects are using ```-fvisibility=default```, meaning there should be no mismatch. Also, no other warnings show up but if I change the project settings I then get hundreds of warnings (again, for details see ticket).  
  
What I find even more puzzling is that I am using all three types of archives (Text, XML and Binary). Of these three, only Text produces this warning. As the problem is with the destructors, I did a quick inventory of the destructors to see how they differ:  
  
```  
    basic_xml_oarchive.hpp: [2]  
    BOOST_ARCHIVE_OR_WARCHIVE_DECL  
    ~basic_xml_oarchive() BOOST_OVERRIDE;  
  
    basic_text_oarchive.hpp: [3]  
    ~basic_text_oarchive() BOOST_OVERRIDE {}  
  
    basic_binary_oarchive.hpp: [4] no destructor.  
```  
  
So my question is, should Text archive also have a ```BOOST_ARCHIVE_OR_WARCHIVE_DECL``` macro, and would that fix my warning? If so I can submit a PR.  
  
Many thanks for your time.  
  
Marco  
  
[0] https://lists.boost.org/boost-users/2022/09/91370.php  
[1] https://github.com/Microsoft/vcpkg/issues/4497  
[2] https://github.com/boostorg/serialization/blob/develop/include/boost/archive/basic_xml_oarchive.hpp  
[3] https://github.com/boostorg/serialization/blob/develop/include/boost/archive/basic_text_oarchive.hpp  
[4] https://github.com/boostorg/serialization/blob/develop/include/boost/archive/basic_binary_oarchive.hpp

---

## Comment 1

> Username: mcraveiro  
> Created at: 2022-09-29 09:48:52 UTC  
> Url: https://github.com/boostorg/serialization/issues/265#issuecomment-1262039664  

In fact this comment also applies to the constructor, I now notice:  
  
```c++  
    BOOST_ARCHIVE_OR_WARCHIVE_DECL void  
    init();  
  
    basic_text_oarchive(unsigned int flags) :  
        detail::common_oarchive<Archive>(flags),  
        delimiter(none)  
    {}  
    ~basic_text_oarchive() BOOST_OVERRIDE {}  
```

---

## Comment 2

> Username: mcraveiro  
> Created at: 2022-09-30 09:54:24 UTC  
> Updated at: 2022-09-30 09:55:15 UTC  
> Url: https://github.com/boostorg/serialization/issues/265#issuecomment-1263360777  

Actually, I was doing this a bit too quickly yesterday :-) I confused ```oarchive``` with ```oprimitive``` I now notice. The problems I report above are with ```oarchive``` but the original linking error is with ```oprimitive```. Looking at this header instead, I see [1]:  
  
```c++  
    BOOST_ARCHIVE_OR_WARCHIVE_DECL  
    basic_text_oprimitive(OStream & os, bool no_codecvt);  
    BOOST_ARCHIVE_OR_WARCHIVE_DECL  
    ~basic_text_oprimitive();  
```  
  
This is a bit annoying as it looks perfectly fine. There is no XML ```oprimitive```, but when I look at the Binary ```oprimitive```, it looks very similar [2]:  
  
```c++  
    BOOST_ARCHIVE_OR_WARCHIVE_DECL  
    basic_binary_oprimitive(  
        std::basic_streambuf<Elem, Tr> & sb,  
        bool no_codecvt  
    );  
    BOOST_ARCHIVE_OR_WARCHIVE_DECL  
    ~basic_binary_oprimitive();  
```  
  
  
[1] https://github.com/boostorg/serialization/blob/develop/include/boost/archive/basic_text_oprimitive.hpp  
[2] https://github.com/boostorg/serialization/blob/develop/include/boost/archive/basic_binary_oprimitive.hpp

---

## Comment 3

> Username: robertramey  
> Created at: 2023-10-23 17:32:42 UTC  
> Url: https://github.com/boostorg/serialization/issues/265#issuecomment-1775684886  

Sooo ... What are you proposing that I do here.  This seems more appropriate as a PR when you get it all figured out.
