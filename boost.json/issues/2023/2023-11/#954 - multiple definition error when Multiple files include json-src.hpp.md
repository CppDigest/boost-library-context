# #954 - multiple definition error when Multiple files include json/src.hpp [Closed]

> Username: hzx1992  
> Created at: 2023-11-20 04:20:49 UTC  
> Updated at: 2024-01-08 11:50:29 UTC  
> Closed at: 2024-01-08 11:50:29 UTC  
> Url: https://github.com/boostorg/json/issues/954  

file_a.cpp  
  
`#include <boost/json/src.hpp>  
  
const boost::json::value toJsonValue() {  
    boost::json::object jsonObj;  
    jsonObj["retentionSeconds"] = "123";  
    jsonObj["shardSeconds"] = "456";  
    std::string jsonStr = boost::json::serialize(jsonObj);  
    return boost::json::parse(jsonStr);  
}`  
  
file_b.cpp  
  
`#include <boost/json/src.hpp>  
  
const boost::json::value toJsonValue2() {  
    boost::json::object jsonObj;  
    jsonObj["retentionSeconds"] = "123";  
    jsonObj["shardSeconds"] = "456";  
    std::string jsonStr = boost::json::serialize(jsonObj);  
    return boost::json::parse(jsonStr);  
}`  
  
  
error  
`/usr/bin/ld: /tmp/ccVsjKLP.o:(.data.rel.local+0x80): multiple definition of `boost::json::detail::default_resource::instance_'; /tmp/ccPVDYEL.o:(.data.rel.local+0x80): first defined here  
/usr/bin/ld: /tmp/ccVsjKLP.o:(.bss+0x0): multiple definition of `boost::json::array::empty_'; /tmp/ccPVDYEL.o:(.bss+0x0): first defined here  
/usr/bin/ld: /tmp/ccVsjKLP.o: in function `boost::json::array::equal(boost::json::array const&) const':  
test_b.cpp:(.text+0x2470): multiple definition of `boost::json::array::equal(boost::json::array const&) const'; /tmp/ccPVDYEL.o:test_a.cpp:(.text+0x2470): first defined here  
/usr/bin/ld: /tmp/ccVsjKLP.o:(.bss+0x10): multiple definition of `boost::json::object::empty_'; /tmp/ccPVDYEL.o:(.bss+0x10): first defined here  
/usr/bin/ld: /tmp/ccVsjKLP.o: in function `boost::json::object::object(std::initializer_list<std::pair<boost::core::basic_string_view<char>, boost::json::value_ref> >, unsigned long, boost::json::storage_ptr)':  
test_b.cpp:(.text+0x3cc6): multiple definition of `boost::json::object::object(std::initializer_list<std::pair<boost::core::basic_string_view<char>, boost::json::value_ref> >, unsigned long, boost::json::storage_ptr)'; /tmp/ccPVDYEL.o:test_a.cpp:(.text+0x3cc6): first defined here  
/usr/bin/ld: /tmp/ccVsjKLP.o: in function `boost::json::object::equal(boost::json::object const&) const':  
test_b.cpp:(.text+0x5178): multiple definition of `boost::json::object::equal(boost::json::object const&) const'; /tmp/ccPVDYEL.o:test_a.cpp:(.text+0x5178): first defined here  
...`

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-11-20 23:34:37 UTC  
> Url: https://github.com/boostorg/json/issues/954#issuecomment-1819974449  

What version of Boost.JSON?

---

## Comment 2

> Username: grisumbras  
> Created at: 2023-11-21 07:01:40 UTC  
> Updated at: 2023-11-21 07:01:55 UTC  
> Url: https://github.com/boostorg/json/issues/954#issuecomment-1820344345  

As per [documentation](https://www.boost.org/doc/libs/1_83_0/libs/json/doc/html/json/overview.html#json.overview.requirements.header_only) you should include `src.hpp` **in exactly one source file**. That file contains definitions. You got "multiple definition" error, because you have multiple definitions.

---

## Comment 3

> Username: grisumbras  
> Created at: 2023-12-28 09:11:10 UTC  
> Url: https://github.com/boostorg/json/issues/954#issuecomment-1870967056  

@hzx1992 have your issue been resolved?

---

## Comment 4

> Username: grisumbras  
> Created at: 2024-01-08 11:50:27 UTC  
> Url: https://github.com/boostorg/json/issues/954#issuecomment-1880853653  

I'm going to consider lack of response as the sign of acknowledgement.
