# #60 - Warnings on use of deprecated Boost Core headers [Closed]

> Username: tony-triller  
> Created at: 2021-05-04 08:17:28 UTC  
> Updated at: 2022-07-11 15:01:57 UTC  
> Closed at: 2022-07-11 15:01:57 UTC  
> Url: https://github.com/boostorg/chrono/issues/60  

If I compile the following with `g++`:  
  
~~~cpp  
#include <boost/chrono/io/duration_io.hpp>  
  
~~~  
  
&hellip;I get compiler warnings:  
  
~~~no-highlight  
In file included from ./boost/config/header_deprecated.hpp:18,  
                 from ./boost/detail/scoped_enum_emulation.hpp:15,  
                 from ./boost/chrono/io/duration_style.hpp:13,  
                 from boost/chrono/io/duration_io.hpp:15:  
./boost/detail/scoped_enum_emulation.hpp:17:1: note: ‘#pragma message: This header is deprecated. Use <boost/core/scoped_enum.hpp> instead.’  
   17 | BOOST_HEADER_DEPRECATED("<boost/core/scoped_enum.hpp>")  
      | ^~~~~~~~~~~~~~~~~~~~~~~  
./boost/detail/no_exceptions_support.hpp:17:1: note: ‘#pragma message: This header is deprecated. Use <boost/core/no_exceptions_support.hpp> instead.’  
   17 | BOOST_HEADER_DEPRECATED("<boost/core/no_exceptions_support.hpp>")  
      | ^~~~~~~~~~~~~~~~~~~~~~~  
  
~~~  
  
(eg see https://godbolt.org/z/3qb6PeWdd )  
  
This shows that Boost Chrono is using deprecated headers. From what I can see, the usages in the current commit are:  
  
* [`include/boost/chrono/io/duration_io.hpp:20`](https://github.com/boostorg/chrono/blob/aa51cbd5121ed29093484f53e5f96e13a9a915b4/include/boost/chrono/io/duration_io.hpp#L20)  
* [`include/boost/chrono/io/duration_style.hpp:13`](https://github.com/boostorg/chrono/blob/aa51cbd5121ed29093484f53e5f96e13a9a915b4/include/boost/chrono/io/duration_style.hpp#L13)  
* [`include/boost/chrono/io/time_point_io.hpp:32`](https://github.com/boostorg/chrono/blob/aa51cbd5121ed29093484f53e5f96e13a9a915b4/include/boost/chrono/io/time_point_io.hpp#L32)  
* [`include/boost/chrono/io/timezone.hpp:12`](https://github.com/boostorg/chrono/blob/aa51cbd5121ed29093484f53e5f96e13a9a915b4/include/boost/chrono/io/timezone.hpp#L12)  
  
It looks like those deprecation warnings were put in place with [this commit](https://github.com/boostorg/core/commit/080e8581d524ed88456ba77bd31b2338f5e1437b) in November 2019.  
  
I realise this isn't a catastrophic problem but it's probably to move off the deprecated headers and the warnings can inject quite a lot of noise into a build's output (and it isn't silenced by `-isystem`).  
  
Thank you very much for all work on this library.
