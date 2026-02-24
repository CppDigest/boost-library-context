# #68 - Why does read_json(..) throw exception for utf-8 stringstream from string? [Closed]

> Username: dkd1111  
> Created at: 2021-02-17 10:11:41 UTC  
> Updated at: 2021-02-18 00:40:11 UTC  
> Closed at: 2021-02-18 00:40:11 UTC  
> Url: https://github.com/boostorg/property_tree/issues/68  

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
std::string msg;  
msg << ss.str();  
ss.clear();  
ss << msg;  
boost::property_tree::ptree tree{};  
boost::property_tree::read_json(ss, tree);  
```  
CODE_2 throws exception("garbage after data").  
  
Variable "ss" is encoded utf-8.  
Why does read_json(..) throw exception for utf-8 stringstream from string?
