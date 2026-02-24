# #129 - Empty query should produce one param [Closed]

> Username: robhobbes  
> Created at: 2022-02-23 19:41:27 UTC  
> Updated at: 2022-09-11 23:53:48 UTC  
> Closed at: 2022-09-11 23:53:47 UTC  
> Url: https://github.com/boostorg/url/issues/129  

I parsed `http://examplebucket.s3.amazonaws.com/test.txt` with `url_view uv = parse_uri(url).value();`. As expected, `uv.has_query()` is false and `uv.encoded_params().size()` is 0. However, a loop (`for (auto p : uv.encoded_params())`) does execute one time.  
  
I'm on commit `8911d05c35123f978e90321fea863385b63525d4`  
  
Here's my example program using gtest:  
```  
#include <gtest/gtest.h>  
#include <boost/url.hpp>  
  
#include <sstream>  
#include <string>  
  
namespace {  
  
using namespace boost::urls;  
using std::string;  
  
TEST(UrlQueryParsing,CheckQueryParams)  
{  
    string url = "http://examplebucket.s3.amazonaws.com/test.txt";  
    url_view uv = parse_uri(url).value();  
    std::cout << "Has query: " << uv.has_query() << ", Size: " << uv.encoded_params().size() << std::endl;  
    std::stringstream ss;  
    for (auto p : uv.encoded_params()) {  
        std::cout << "Key: " << p.key << ", Value: " << p.value << std::endl;  
        ss << "Key: " << p.key << ", Value: " << p.value << std::endl;  
    }  
    EXPECT_EQ(ss.str(), string(""));  
}  
  
}  
```  
  
Output:  
```  
Has query: 0, Size: 0  
Key: , Value:  
tests/url_query_parsing.cpp:22: Failure  
Expected equality of these values:  
  ss.str()  
    Which is: "Key: , Value: \n"  
  string("")  
    Which is: ""  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-02-23 19:42:09 UTC  
> Url: https://github.com/boostorg/url/issues/129#issuecomment-1049146947  

darnit... you're right - that's not supposed to happen !!!!!!!!

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-02-23 19:58:50 UTC  
> Url: https://github.com/boostorg/url/issues/129#issuecomment-1049160109  

This is because, in the URL `http://example.com/index.htm?` there is one query param whose key and value are empty. So the code handles this case. However, the caller has to check that the size of the params container is not zero, as otherwise there is no way to tell if an empty string represents a non-existing query (size==0) or a query with one empty param (size==1).  
  
In other words, it was a bug :) Thanks for reporting this, these containers were a chore to get right.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-02-23 20:19:23 UTC  
> Url: https://github.com/boostorg/url/issues/129#issuecomment-1049177851  

should be fixed in master/develop now

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-08-27 20:05:50 UTC  
> Url: https://github.com/boostorg/url/issues/129#issuecomment-1229257747  

Unfortunately this was resolved incorrectly. The relative-ref `"?"` should produce exactly one query parameter, with an empty key and no value. Otherwise, if it was to produce zero parameters, then it would not be distinguishable from the relative-ref `""` and round-trips would be lossy.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-09-11 23:53:47 UTC  
> Url: https://github.com/boostorg/url/issues/129#issuecomment-1243071923  

`url_view( "?" )` produces one key/value pair with an empty key and no value  
  
while `parse_params( "" )` produces no key/value pairs
