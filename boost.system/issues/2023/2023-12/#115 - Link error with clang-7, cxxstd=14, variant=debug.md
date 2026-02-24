# #115 - Link error with clang-7, cxxstd=14, variant=debug [Closed]

> Username: anarthal  
> Created at: 2023-12-14 14:03:40 UTC  
> Updated at: 2023-12-15 23:33:46 UTC  
> Closed at: 2023-12-15 13:15:40 UTC  
> Url: https://github.com/boostorg/system/issues/115  

How to reproduce:  
  
* In a machine with clang-7 (in my case, I'm using `docker run --rm -it ghcr.io/anarthal-containers/build-clang7:d34a92a9b9702a0a5e2b9ab4a635f7b077229626`  
* Build Boost.URL, as per `./b2 toolset=clang variant=debug cxxstd=14 --with-url --prefix=/opt/myboost/ install`  
* Create a sample Boost.URL application, as per [this gist](https://gist.github.com/anarthal/ab9f5451f4df35753d2497dab9abb953).  
* Build, as per `mkdir build && cd build && cmake -DCMAKE_PREFIX_PATH=/opt/myboost/ && make`.  
  
Failing with the following error (looks like in C++14, `static constexpr` variables are not handled correctly in some cases):  
  
```  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<unsigned short, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::detail::userinfo_rule_t::value_type, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::detail::h16_rule_t::value_type, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::detail::ipvfuture_rule_t::value_type, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::detail::ipv6_addrz_rule_t::value_type, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::detail::port_part_rule_t::value_type, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::optional<boost::urls::detail::userinfo_template_rule_t::value_type>, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::detail::port_rule::value_type, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::detail::hier_part_rule_t::value_type, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::variant2::variant<unsigned long, boost::optional<boost::variant2::variant<boost::core::basic_string_view<char>, unsigned long> > >, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<unsigned char, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::params_encoded_view, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::optional<boost::core::basic_string_view<char> >, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<std::tuple<unsigned char, unsigned char, unsigned char, unsigned char>, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::core::basic_string_view<char>, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::variant2::variant<boost::urls::url_view, boost::urls::url_view>, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::variant2::variant<boost::urls::url_view, boost::urls::url_view>, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<unsigned short, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::optional<boost::variant2::variant<boost::core::basic_string_view<char>, unsigned long> >, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<std::tuple<boost::core::basic_string_view<char>, boost::urls::pct_string_view>, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::ipv4_address, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::grammar::range<std::tuple<boost::core::basic_string_view<char>, boost::urls::pct_string_view> >, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::segments_encoded_view, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<std::tuple<boost::core::basic_string_view<char>, boost::optional<boost::core::basic_string_view<char> > >, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::optional<boost::urls::pct_string_view>, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::segments_encoded_view, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::authority_view, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::ipv4_address, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::detail::h16_rule_t::value_type, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::detail::pattern, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::pct_string_view, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::detail::ip_literal_rule_t::value_type, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::detail::host_rule_t::value_type, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::detail::hier_part_rule_t::value_type, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::ipv6_address, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<std::tuple<boost::core::basic_string_view<char>, boost::urls::pct_string_view>, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::core::basic_string_view<char>, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<std::tuple<boost::core::basic_string_view<char>, boost::optional<boost::core::basic_string_view<char> > >, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::detail::scheme_rule::value_type, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::variant2::variant<unsigned long, boost::optional<boost::variant2::variant<boost::core::basic_string_view<char>, unsigned long> > >, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::detail::fragment_part_rule_t::value_type, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::pct_string_view, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::variant2::variant<boost::core::basic_string_view<char>, unsigned long>, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::detail::scheme_rule::value_type, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::detail::query_part_rule_t::value_type, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::url_view, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<void, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<std::tuple<boost::core::basic_string_view<char>, boost::core::basic_string_view<char>, boost::core::basic_string_view<char>, boost::core::basic_string_view<char> >, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::detail::port_part_rule_t::value_type, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::params_encoded_view, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::detail::userinfo_template_rule_t::value_type, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<std::tuple<boost::optional<boost::variant2::variant<boost::core::basic_string_view<char>, unsigned long> >, boost::optional<boost::core::basic_string_view<char> > >, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::detail::relative_part_rule_t::value_type, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::optional<boost::variant2::variant<boost::core::basic_string_view<char>, unsigned long> >, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::grammar::range<std::tuple<boost::core::basic_string_view<char>, boost::urls::pct_string_view> >, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<unsigned long, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::detail::pattern, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::detail::userinfo_rule_t::value_type, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::optional<boost::urls::pct_string_view>, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::detail::ipv6_addrz_rule_t::value_type, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::detail::fragment_part_rule_t::value_type, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::optional<boost::urls::detail::userinfo_rule_t::value_type>, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::detail::port_rule::value_type, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::variant2::variant<boost::core::basic_string_view<char>, unsigned long>, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::url_view, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::detail::host_rule_t::value_type, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::detail::userinfo_template_rule_t::value_type, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::detail::ipvfuture_rule_t::value_type, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::ipv6_address, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<std::tuple<unsigned char, unsigned char, unsigned char, unsigned char>, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::detail::relative_part_rule_t::value_type, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::optional<boost::core::basic_string_view<char> >, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<unsigned char, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::detail::query_part_rule_t::value_type, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::authority_view, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<void, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<std::tuple<boost::core::basic_string_view<char>, boost::core::basic_string_view<char>, boost::core::basic_string_view<char>, boost::core::basic_string_view<char> >, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<boost::urls::detail::ip_literal_rule_t::value_type, boost::system::error_code>::in_place_error'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<std::tuple<boost::optional<boost::variant2::variant<boost::core::basic_string_view<char>, unsigned long> >, boost::optional<boost::core::basic_string_view<char> > >, boost::system::error_code>::in_place_value'  
/usr/bin/ld: /opt/myboost/lib/libboost_url.so.1.84.0: undefined reference to `boost::system::result<unsigned long, boost::system::error_code>::in_place_error'  
clang: error: linker command failed with exit code 1 (use -v to see invocation)  
```

---

## Comment 1

> Username: alandefreitas  
> Created at: 2023-12-14 14:18:11 UTC  
> Updated at: 2023-12-14 14:18:21 UTC  
> Url: https://github.com/boostorg/system/issues/115#issuecomment-1856955320  

Man... C++ is complex 😆   
  
I can't find the difference between this and what we test in https://github.com/boostorg/url/actions/runs/7173027751/job/19531625406#step:15:1  
  
I'll ask around

---

## Comment 2

> Username: alandefreitas  
> Created at: 2023-12-14 14:38:08 UTC  
> Url: https://github.com/boostorg/system/issues/115#issuecomment-1856955337  

Do you have the complete workflow somewhere?

---

## Comment 3

> Username: pdimov  
> Created at: 2023-12-14 14:43:23 UTC  
> Url: https://github.com/boostorg/system/issues/115#issuecomment-1856955348  

Is this specific to clang-7?

---

## Comment 4

> Username: pdimov  
> Created at: 2023-12-14 14:48:03 UTC  
> Url: https://github.com/boostorg/system/issues/115#issuecomment-1856955355  

Interestingly, in_place_value and in_place_error aren't even used anywhere in Boost.URL.

---

## Comment 5

> Username: pdimov  
> Created at: 2023-12-14 14:52:32 UTC  
> Url: https://github.com/boostorg/system/issues/115#issuecomment-1856955362  

One thing to note is that you're building Boost explicitly with C++14, but your program doesn't set a C++ standard in CMakeLists. What C++ standard is being used while building it?

---

## Comment 6

> Username: anarthal  
> Created at: 2023-12-14 21:11:30 UTC  
> Url: https://github.com/boostorg/system/issues/115#issuecomment-1856955371  

I wrote the CML as a minimal reproducible example. The actual failure was encountered while linking a test executable against `/boost/url//boost_url` (everything happening in `b2`), in MySQL CIs. It doesn't happen in release mode.  
  
The run is [here](https://drone.cpp.al/boostorg/mysql/420/12/3). I don't do the entire clang coverage range, but doesn't happen in clang 3.6 or clang 11.

---

## Comment 7

> Username: pdimov  
> Created at: 2023-12-14 23:30:05 UTC  
> Url: https://github.com/boostorg/system/issues/115#issuecomment-1856955376  

OK, this is clearly a System issue, but I have no idea why it happens in the way it happens here, as these symbols don't seem to be referenced anywhere in URL. I do have an idea what I need to do to fix it, though. :-)

---

## Comment 8

> Username: alandefreitas  
> Created at: 2023-12-15 15:33:14 UTC  
> Url: https://github.com/boostorg/system/issues/115#issuecomment-1858069479  

I have no idea what just happened here 🤣

---

## Comment 9

> Username: pdimov  
> Created at: 2023-12-15 15:51:49 UTC  
> Url: https://github.com/boostorg/system/issues/115#issuecomment-1858097058  

I transferred the issue to System and fixed it. Or I think that I fixed it. :-)

---

## Comment 10

> Username: pdimov  
> Created at: 2023-12-15 17:57:31 UTC  
> Url: https://github.com/boostorg/system/issues/115#issuecomment-1858275602  

@anarthal Can you test develop and see if the issue is fixed?

---

## Comment 11

> Username: anarthal  
> Created at: 2023-12-15 23:33:45 UTC  
> Url: https://github.com/boostorg/system/issues/115#issuecomment-1858608230  

I can confirm this fixed the issue. Thanks.
