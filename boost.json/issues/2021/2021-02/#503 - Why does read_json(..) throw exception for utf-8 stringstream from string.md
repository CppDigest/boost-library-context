# #503 - Why does read_json(..) throw exception for utf-8 stringstream from string? [Closed]

> Username: dkd1111  
> Created at: 2021-02-17 10:16:01 UTC  
> Updated at: 2021-02-17 13:20:18 UTC  
> Closed at: 2021-02-17 13:20:18 UTC  
> Url: https://github.com/boostorg/json/issues/503  

```  
/*CODE_1*/  
boost::beast::http::response<boost::beast::http::string_body> &&res  
...  
std::stringstream ss;  
ss << res.body();  
boost::property_tree::ptree tree{};  
boost::property_tree::read_json(ss, tree);  
```  
CODE_1 is fine.  
  
```  
/*CODE_2*/  
boost::beast::http::response<boost::beast::http::string_body> &&res  
...  
std::stringstream ss;  
ss << res.body();  
std::string msg = ss.str();  
ss.clear();  
ss << msg;  
boost::property_tree::ptree tree{};  
boost::property_tree::read_json(ss, tree);  
```  
CODE_2 throws exception("garbage after data").  
  
Variable "ss" is encoded utf-8.  
Why does read_json(..) throw exception for utf-8 stringstream from string?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-02-17 12:31:32 UTC  
> Url: https://github.com/boostorg/json/issues/503#issuecomment-780525138  

> Why does read_json(..) throw exception for utf-8 stringstream from string?  
  
`read_json` is from Boost.PropertyTree, not Boost.JSON. You are using the wrong library. If you want to use Boost.JSON you need to use `boost::json::parse`. See:  
  
https://www.boost.org/doc/libs/develop/libs/json/doc/html/index.html

---

## Comment 2

> Username: dkd1111  
> Created at: 2021-02-17 13:20:16 UTC  
> Url: https://github.com/boostorg/json/issues/503#issuecomment-780550984  

ok. thanks.
