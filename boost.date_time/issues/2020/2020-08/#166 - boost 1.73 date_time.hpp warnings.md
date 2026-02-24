# #166 - boost 1.73 date_time.hpp warnings [Closed]

> Username: SPlatten  
> Created at: 2020-08-04 05:36:24 UTC  
> Updated at: 2020-08-04 16:37:23 UTC  
> Closed at: 2020-08-04 16:37:23 UTC  
> Url: https://github.com/boostorg/date_time/issues/166  

I'm working on porting a C++ project originally using boost 1.48 now I'm updating to boost 1.73, I've got a single CPP file with just:  
  
``  
#include <boost/date_time.hpp>  
``  
  
When I compile this file I get lots of warnings, can anyone help?  As the file only contains this single line I'm confident its only in boost.  I'm using MSVC 2017.  
  
``  
------ Build started: Project: Debug Service Group, Configuration: Debug Win32 ------  
debugengine.cpp  
c:\program files (x86)\microsoft visual studio\2017\professional\vc\tools\msvc\14.16.27023\include\utility(173): warning C4244: 'initializing': conversion from '_Ty' to '_Ty2', possible loss of data  
        with  
        [  
            _Ty=int  
        ]  
        and  
        [  
            _Ty2=unsigned short  
        ]  
c:\boost\boost_1_73_0\boost\date_time\date_parsing.hpp(111): note: see reference to function template instantiation 'std::pair<const _Kty,_Ty>::pair<const char(&)[4],int,0>(_Other1,_Other2 &&) noexcept(false)' being compiled  
        with  
        [  
            _Kty=std::string,  
            _Ty=unsigned short,  
            _Other1=const char (&)[4],  
            _Other2=int  
        ]  
c:\boost\boost_1_73_0\boost\date_time\date_parsing.hpp(99): note: see reference to function template instantiation 'std::pair<const _Kty,_Ty>::pair<const char(&)[4],int,0>(_Other1,_Other2 &&) noexcept(false)' being compiled  
        with  
        [  
            _Kty=std::string,  
            _Ty=unsigned short,  
            _Other1=const char (&)[4],  
            _Other2=int  
        ]  
c:\boost\boost_1_73_0\boost\date_time\date_parsing.hpp(168): note: see reference to function template instantiation 'unsigned short boost::date_time::month_str_to_ushort<month_type>(const std::string &)' being compiled  
c:\boost\boost_1_73_0\boost\date_time\gregorian\parsers.hpp(49): note: see reference to function template instantiation 'date_type boost::date_time::parse_date<boost::gregorian::date>(const std::string &,int)' being compiled  
        with  
        [  
            date_type=boost::gregorian::date  
        ]  
Done building project "Debug Service Group.vcxproj".  
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========  
``

---

## Comment 1

> Username: JeffGarland  
> Created at: 2020-08-04 16:37:23 UTC  
> Url: https://github.com/boostorg/date_time/issues/166#issuecomment-668701374  

This will be fixed in 1.74 which is in final beta. -- see issue #148.  In the meantime you can turn warning level down to 3 or believe the patch you want is all in date_parsing.hpp.    
  
https://github.com/boostorg/date_time/commit/ab066e360173cd7445073f6ebf31cd982a557d5e#diff-90a09507c48484c9614ad7ec3d882cda
