# #50 - Failures when build boost with MSVC+ /permissive- [Closed]

> Username: PhoebeHui  
> Created at: 2017-10-26 03:45:36 UTC  
> Updated at: 2017-12-18 15:36:52 UTC  
> Closed at: 2017-10-26 04:05:33 UTC  
> Url: https://github.com/boostorg/format/issues/50  

Boost test ‘test_json_parser’ in libs\property_tree\test failed with error C2589 C2760 when build with /permissive- in windows, this is source issue looks involved by https://github.com/boostorg/format/commit/bc53dbbaa235031954a854f87cdf04e1103e2a6d#diff-67bfd7972eb56b5161d31780dbaa62a2   
  
The source includes windows.h which defines the macro 'min' and 'max'. It need define ‘NOMINMAX’ before include ‘windows.h‘.  
  
Environment: windows server 2016 + Visual studio 2017 update2 + Boost( master branch with latest revision ‘b30e350’)  
  
Failures like:  
test_json_parser.cpp  
Info: Boost.Config is older than your compiler version - probably nothing bad will happen - but you may wish to look for an update Boost version.  Define BOOST_CONFIG_SUPPRESS_OUTDATED_MESSAGE to suppress this message.  
.\boost/format/feed_args.hpp(205): warning C4003: not enough actual parameters for macro 'max'  
.\boost/format/feed_args.hpp(250): warning C4003: not enough actual parameters for macro 'max'  
.\boost/format/feed_args.hpp(205): error C2589: '(': illegal token on right side of '::'  
.\boost/format/feed_args.hpp(205): error C2760: syntax error: unexpected token '(', expected ')'

---

## Comment 1

> Username: jeking3  
> Created at: 2017-10-26 03:57:47 UTC  
> Updated at: 2017-10-26 04:00:10 UTC  
> Url: https://github.com/boostorg/format/issues/50#issuecomment-339544292  

Resolved on October 24:   
  
https://github.com/boostorg/format/commit/ef8ce677b0716f882221d0f2de2d56c14937ca9c#diff-3e58adef3e949129f16441f608d7817f  
  
I see, this wasn't merged to master yet.

---

## Comment 2

> Username: jeking3  
> Created at: 2017-10-26 04:05:33 UTC  
> Url: https://github.com/boostorg/format/issues/50#issuecomment-339545225  

master has been refreshed with the fix - closing

---

## Comment 3

> Username: PhoebeHui  
> Created at: 2017-10-26 05:35:35 UTC  
> Url: https://github.com/boostorg/format/issues/50#issuecomment-339556606  

Thank you for these quickly fix!
