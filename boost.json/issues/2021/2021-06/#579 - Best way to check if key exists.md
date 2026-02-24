# #579 - Best way to check if key exists? [Closed]

> Username: KordianD  
> Created at: 2021-06-09 14:29:41 UTC  
> Updated at: 2022-11-16 12:29:24 UTC  
> Closed at: 2022-11-16 12:29:24 UTC  
> Url: https://github.com/boostorg/json/issues/579  

What is the best way to check if a given key exists in json?  
  
Json example  
  
    {  
        "1":{  
           "key":"A"  
        }  
     }  
  
I want to parse key `missingKey` but I do not know whether it exists in json.   
  
    #include <string>  
    #include <boost/json/src.hpp>  
  
    int main() {  
  
        const std::string json = "{"  
                                 "\"1\":{"  
                                 "\"key\": 10"  
                                 "}}";  
  
       const boost::json::value jv = boost::json::parse(json);  
       const boost::json::object& rootJsonObject = jv.as_object();  
  
       for (const auto& jsonIdRoot : rootJsonObject)  
       {  
         const auto& jsonObject = jsonIdRoot.value();  
         jsonObject.at("missingKey").as_int64();  
       }   
    }  
  
  
This will throw exception because "missingKey" is missing.   
  
Is there any exists/contains etc method to do this?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-06-09 16:49:14 UTC  
> Url: https://github.com/boostorg/json/issues/579#issuecomment-857863427  

This is one easy way:  
  
https://www.boost.org/doc/libs/develop/libs/json/doc/html/json/ref/boost__json__object/if_contains.html  
```  
if( p = jsonObject.if_contains( "missingKey" ) )  
```  
  
You could also use `object::find`  
https://www.boost.org/doc/libs/develop/libs/json/doc/html/json/ref/boost__json__object/find/overload1.html

---

## Comment 2

> Username: grisumbras  
> Created at: 2021-06-18 11:31:32 UTC  
> Url: https://github.com/boostorg/json/issues/579#issuecomment-863971583  

@KordianD does Vinnie's comment answer your question?
