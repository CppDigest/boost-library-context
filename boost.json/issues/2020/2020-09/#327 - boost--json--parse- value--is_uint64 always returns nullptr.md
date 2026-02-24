# #327 - boost::json::parse: value::is_uint64 always returns nullptr [Closed]

> Username: Ariox41  
> Created at: 2020-09-13 14:25:07 UTC  
> Updated at: 2020-09-26 02:23:50 UTC  
> Closed at: 2020-09-26 02:23:49 UTC  
> Url: https://github.com/boostorg/json/issues/327  

```c++  
#define DOCTEST_CONFIG_IMPLEMENT_WITH_MAIN  
#include "doctest.h"  
#define BOOST_JSON_STANDALONE  
#include <boost/json/src.hpp>  
#include <de/boost_json_de.hpp>  
TEST_CASE("boost json unsigned test ") {  
    constexpr boost::json::string_view json_string = R"({ "int": -21, "unsigned": 2 })";  
    boost::json::error_code     ec;  
    auto root = boost::json::parse(json_string, ec);  
    if (ec) {  
        auto message = ec.message();  
        MESSAGE(doctest::String(message.data(), message.size()));  
    }  
    REQUIRE(!ec);  
  
    REQUIRE(root.is_object());  
  
    auto&& obj       = root.get_object();  
    auto&& int_      = obj.find("int");  
    auto&& unsigned_ = obj.find("unsigned");  
  
    REQUIRE(int_ != obj.end());     // ok  
    REQUIRE(unsigned_ != obj.end());// ok  
  
    REQUIRE(int_->value().is_int64());// ok  
    CHECK(int_->value().is_uint64() == nullptr);// ok  
    CHECK(int_->value().get_int64() == -21);// ok  
  
    CHECK(unsigned_->value().is_int64()); // ok  
    REQUIRE(unsigned_->value().is_uint64()); // err  
    CHECK(unsigned_->value().get_uint64() == 2);   
}  
```  
The documentation also contains an error  
  
  
is_uint64 | Return a uint64_t                   pointer if this is a signed integer, else return nullptr  
-- | --

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-09-13 14:27:36 UTC  
> Url: https://github.com/boostorg/json/issues/327#issuecomment-691678413  

2 is a signed integer.  
  
The documentation has a typo, I will update it - thanks!

---

## Comment 2

> Username: sdkrystian  
> Created at: 2020-09-13 14:33:01 UTC  
> Url: https://github.com/boostorg/json/issues/327#issuecomment-691679052  

@vinniefalco I fixed this in the javadoc on the doc review PR

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-09-26 02:23:49 UTC  
> Url: https://github.com/boostorg/json/issues/327#issuecomment-699277345  

The behavior is correct. Boost.JSON's parser emits integers as signed unless the number would exceed the INT64_MAX, or unless you explicitly tell it to use a uint64. For example by writing:  
```  
value jv( 1u ); // unsigned  
```
