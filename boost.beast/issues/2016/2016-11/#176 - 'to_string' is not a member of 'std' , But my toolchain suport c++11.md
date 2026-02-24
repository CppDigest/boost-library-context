# #176 - 'to_string' is not a member of 'std' , But my toolchain suport c++11 [Closed]

> Username: Pillar1989  
> Created at: 2016-11-09 08:39:57 UTC  
> Updated at: 2016-11-10 06:47:00 UTC  
> Closed at: 2016-11-10 06:47:00 UTC  
> Url: https://github.com/boostorg/beast/issues/176  

Hi , I want to cross compile beast to openwrt. But It's compiled errior.  
```  
In file included from /home/pillar/MTK/openwrt/build_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/beast/include/beast/http/message.hpp:536:0,  
                 from /home/pillar/MTK/openwrt/build_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/beast/include/beast/http/basic_parser_v1.hpp:11,  
                 from /home/pillar/MTK/openwrt/build_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/beast/include/beast/http.hpp:12,  
                 from /home/pillar/MTK/openwrt/build_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/beast/examples/http_crawl.cpp:11:  
/home/pillar/MTK/openwrt/build_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/beast/include/beast/http/impl/message.ipp: In member function 'void beast::http::prepare(beast::http::message<isRequest, Body, Headers>&, Options&& ...)::set_field::operator()(beast::http::message<true, Body, Headers>&, const beast::http::detail::prepare_info&) const':  
/home/pillar/MTK/openwrt/build_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/beast/include/beast/http/impl/message.ipp:166:29: error: 'to_string' is not a member of 'std'  
                             std::to_string(*pi.content_length));  
                             ^  
/home/pillar/MTK/openwrt/build_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/beast/include/beast/http/impl/message.ipp: In member function 'void beast::http::prepare(beast::http::message<isRequest, Body, Headers>&, Options&& ...)::set_field::operator()(beast::http::message<false, Body, Headers>&, const beast::http::detail::prepare_info&) const':  
/home/pillar/MTK/openwrt/build_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/beast/include/beast/http/impl/message.ipp:179:29: error: 'to_string' is not a member of 'std'  
                             std::to_string(*pi.content_length));  
  
```  
  
There is a way support g++ doesnt have to_string. But a lot of code need include <beast/core/to_string.hpp>  
```c++  
//  
// Copyright (c) 2013-2016 Vinnie Falco (vinnie dot falco at gmail dot com)  
//  
// Distributed under the Boost Software License, Version 1.0. (See accompanying  
// file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
//  
  
#ifndef BEAST_TO_STRING_HPP  
#define BEAST_TO_STRING_HPP  
  
#include <beast/core/buffer_concepts.hpp>  
#include <boost/asio/buffer.hpp>  
#include <boost/lexical_cast.hpp>  
#include <string>  
  
namespace beast {  
  
/** Convert a @b `ConstBufferSequence` to a `std::string`.  
  
    This function will convert the octets in a buffer sequence to a string.  
    All octets will be inserted into the resulting string, including null  
    or unprintable characters.  
  
    @param buffers The buffer sequence to convert.  
  
    @return A string representing the contents of the input area.  
  
    @note This function participates in overload resolution only if  
    the buffers parameter meets the requirements of @b `ConstBufferSequence`.  
*/  
template<class ConstBufferSequence>  
#if GENERATING_DOCS  
std::string  
#else  
typename std::enable_if<  
    is_ConstBufferSequence<ConstBufferSequence>::value,  
        std::string>::type  
#endif  
to_string(ConstBufferSequence const& buffers)  
{  
    using boost::asio::buffer_cast;  
    using boost::asio::buffer_size;  
    std::string s;  
    s.reserve(buffer_size(buffers));  
    for(auto const& buffer : buffers)  
        s.append(buffer_cast<char const*>(buffer),  
            buffer_size(buffer));  
    return s;  
}  
  
} // beast  
  
#endif  
  
#ifndef HAVE_STD_TO_STRING  
namespace std {  
template<typename V>  
inline std::string  
to_string(const V& v)  
{  
  return boost::lexical_cast<std::string>(v);  
}  
} // namespace std  
#endif // HAVE_STD_TO_STRING  
  
```  
  
Do you have any other better idea ? or how to solve this compile issue ?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-11-09 11:36:34 UTC  
> Url: https://github.com/boostorg/beast/issues/176#issuecomment-259394592  

If your g++ does not have `std::to_string`, then it isn't really C++11 compatible. This article explains the problem and provides solutions:  
http://stackoverflow.com/questions/12975341/to-string-is-not-a-member-of-std-says-g

---

## Comment 2

> Username: Pillar1989  
> Created at: 2016-11-09 12:29:19 UTC  
> Url: https://github.com/boostorg/beast/issues/176#issuecomment-259403805  

I also test it on Ubuntu 14.04 and debian 7， It has gcc 4.8. unfortunatly, I can not get to_string function.  It means Beast can't be used a lot of device.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2016-11-09 12:30:11 UTC  
> Url: https://github.com/boostorg/beast/issues/176#issuecomment-259403950  

Is `std::to_string` the only problem with gcc 4.8?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2016-11-09 15:41:56 UTC  
> Url: https://github.com/boostorg/beast/issues/176#issuecomment-259445237  

I have changed all the calls from `std::to_string` to `boost::lexical_cast<std::string>` please try it at https://github.com/vinniefalco/Beast/commit/9dd70f5f732dcdfca933cb40406dea8527d94698

---

## Comment 5

> Username: Pillar1989  
> Created at: 2016-11-10 06:47:00 UTC  
> Url: https://github.com/boostorg/beast/issues/176#issuecomment-259614352  

Thanks, It works now.
