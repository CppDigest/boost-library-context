# #25 - Describe and json serialization with containers [Closed]

> Username: vricosti  
> Created at: 2021-11-26 14:54:44 UTC  
> Updated at: 2022-02-13 01:48:34 UTC  
> Closed at: 2022-02-13 01:48:34 UTC  
> Url: https://github.com/boostorg/describe/issues/25  

Hello,  
  
Let's say I have the following struct:  
  
```  
struct Item  
   {  
      std::string id;  
      std::string label;  
      std::string iconFile;  
   };  
   BOOST_DESCRIBE_STRUCT(Item, (), (id, label, iconFile))  
```  
When I serialize in json the following code:  
  
```  
Item item = { "anId", "aLabel", "anIcon"};  
 std::stringstream ss;  
 ss << boost::json::value_from(item);  
```  
I get the following json:  
  
`{"id":"anId","label":"aLabel","iconFile":"anIcon"}`  
  
Would it be possible to have directly:  
  
`{ "item": {"id":"anId","label":"aLabel","iconFile":"anIcon"} }`  
  
Without having to write:  
  
```  
boost::json::object jObj;  
jObj["item"] = boost::json::value_from(item);  
auto msg = boost::json::serialize(jObj);  
```  
  
Thanks

---

## Comment 1

> Username: pdimov  
> Created at: 2021-11-26 15:01:17 UTC  
> Url: https://github.com/boostorg/describe/issues/25#issuecomment-980042497  

The JSON you want corresponds to a struct having `Item` as the only member, something like  
```  
struct X  
{  
    Item item;  
};  
BOOST_DESCRIBE_STRUCT(X, (), (item))  
```
