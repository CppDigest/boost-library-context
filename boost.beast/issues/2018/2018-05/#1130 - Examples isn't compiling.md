# #1130 - Examples isn't compiling [Closed]

> Username: yuyaryshev  
> Created at: 2018-05-16 08:21:31 UTC  
> Updated at: 2018-06-22 17:05:05 UTC  
> Closed at: 2018-06-22 17:05:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1130  

### Version of Beast  
#define BOOST_BEAST_VERSION 167  
  
### Steps necessary to reproduce the problem  
Trying to compile http_server_small.cpp  
  
### All relevant compiler information  
The example don't compile. It gives three errors in header files.  
  
The first error - d:\boost\boost\beast\core\detail\ostream.hpp(263)  
doesn't seem to be able compile in any C++ environment, because it's really missing template arguments and I just fixed it locally by adding them.  
  
Visual studio 2017 - v171 x64  
1>------ Build started: Project: test, Configuration: Debug Win32 ------  
1>stdafx.cpp  
1>d:\boost\boost\beast\core\detail\ostream.hpp(263): error C2955: 'boost::beast::detail::ostream_helper': use of class template requires template argument list  
1>d:\boost\boost\beast\core\detail\ostream.hpp(234): note: see declaration of 'boost::beast::detail::ostream_helper'  
1>d:\boost\boost\beast\core\detail\ostream.hpp(264): error C2244: 'boost::beast::detail::ostream_helper<DynamicBuffer,CharT,Traits,true>::ostream_helper': unable to match function definition to an existing declaration  
1>d:\boost\boost\beast\core\detail\ostream.hpp(262): note: see declaration of 'boost::beast::detail::ostream_helper<DynamicBuffer,CharT,Traits,true>::ostream_helper'  
1>d:\boost\boost\beast\core\detail\ostream.hpp(264): note: definition  
1>d:\boost\boost\beast\core\detail\ostream.hpp(264): note: 'boost::beast::detail::ostream_helper<DynamicBuffer,CharT,Traits,true>::ostream_helper(boost::beast::detail::ostream_helper &&)'  
1>d:\boost\boost\beast\core\detail\ostream.hpp(264): note: existing declarations  
1>d:\boost\boost\beast\core\detail\ostream.hpp(264): note: 'boost::beast::detail::ostream_helper<DynamicBuffer,CharT,Traits,true>::ostream_helper(boost::beast::detail::ostream_helper<DynamicBuffer,CharT,Traits,true> &&)'  
1>d:\boost\boost\beast\core\detail\ostream.hpp(264): note: 'boost::beast::detail::ostream_helper<DynamicBuffer,CharT,Traits,true>::ostream_helper(DynamicBuffer &)'

---

## Comment 1

> Username: snahmad  
> Created at: 2018-05-16 09:59:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1130#issuecomment-389464343  

Try removing use of stdafx.h from your .cpp file properties.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-05-16 16:23:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1130#issuecomment-389580645  

This looks like a configuration problem on your end because  
  
1. Yes, it is valid C++  
2. I have no trouble compiling it using Visual Studio 2017 latest, in all configurations, and  
3. the AppVeyor scripts regularly compile the library and examples, and have no trouble compiling it: https://ci.appveyor.com/project/vinniefalco/beast/history

---

## Comment 3

> Username: stale[bot]  
> Created at: 2018-06-15 17:02:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1130#issuecomment-397683473  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: stale[bot]  
> Created at: 2018-06-22 17:05:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1130#issuecomment-399512761  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
