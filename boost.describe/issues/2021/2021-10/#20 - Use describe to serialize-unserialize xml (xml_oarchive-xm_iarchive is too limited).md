# #20 - Use describe to serialize/unserialize xml (xml_oarchive/xm_iarchive is too limited) [Closed]

> Username: vricosti  
> Created at: 2021-10-11 17:59:51 UTC  
> Updated at: 2022-02-13 01:47:40 UTC  
> Closed at: 2022-02-13 01:47:40 UTC  
> Url: https://github.com/boostorg/describe/issues/20  

Hi,  
  
I am using describe to serialize.unserialize some structs to json (thanks to boost::json) but now I would also be able to do it in xml.  
I tried boost::archive::: (xml_oarchive/xml_iarchive) but the resulting xml always have some useless nodes (<count> <xxx_version>) that cannot be easily removed from what I understand.  
Let's consider the following code:  
  
```  
namespace JsonSerializer  
{  
   struct Item  
   {  
      std::string id;  
      std::string label;  
      std::string iconFile;  
   };  
   BOOST_DESCRIBE_STRUCT(Item, (), (id, label, iconFile))  
  
   struct Items  
   {  
      using Type = std::vector<Item>;  
      Type items;  
   };  
   BOOST_DESCRIBE_STRUCT(Items, (), (items));  
  
} // namespace JsonSerializer  
  
  
// Lines below allows to not add bunch of ugly tags/info when serializing in xml  
BOOST_CLASS_IMPLEMENTATION(JsonSerializer::Item, boost::serialization::object_serializable);  
BOOST_CLASS_IMPLEMENTATION(JsonSerializer::Items::Type, boost::serialization::object_serializable);  
BOOST_CLASS_IMPLEMENTATION(JsonSerializer::Items, boost::serialization::object_serializable);  
```  
When I try fill an Items object and I serialize here is the result:  
  
```  
Items items;  
// ... some code to fill items list ...  
std::ostringstream os;  
unsigned int flags = boost::archive::no_header;  
boost::archive::xml_oarchive ar(os, flags);  
ar << boost::make_nvp("items", items.items);  
std::string sXml = os.str();  
```  
  
```  
<items>  
	<count>1</count>  
	<item_version>0</item_version>  
	<item>  
		<id>standalone</id>  
		<label>StandAlone</label>  
		<iconFile>Logo_StandAlone.png</iconFile>  
	</item>  
</items>  
```  
Even with all the ugly BOOST_CLASS_IMPLEMENTATION macros and the use of boost::archive::no_header, the result is not satisfying (useless item_version, count), so where should I start to get a clean xml ?

---

## Comment 1

> Username: pdimov  
> Created at: 2021-10-11 18:40:32 UTC  
> Updated at: 2021-10-11 18:40:54 UTC  
> Url: https://github.com/boostorg/describe/issues/20#issuecomment-940354863  

`<count>` and `<item_version>` are emitted by the serialization of `std::vector`. Looking at the Boost.Serialization source, I don't see any way to turn them off. You might want to ask this question in an issue in `boostorg/serialization` and see if @robertramey has something to offer as an advice.  
  
I'm not sure that it would be possible to avoid the `<count>` due to the architecture of Boost.Serialization, which on reading needs to know how many elements to load. I don't know what's the purpose of `<item_version>`. It's possible that it's needed for optimized saving and loading of arrays of primitive types, but only Robert can say for sure.

---

## Comment 2

> Username: vricosti  
> Created at: 2021-10-11 19:01:47 UTC  
> Url: https://github.com/boostorg/describe/issues/20#issuecomment-940365958  

Ok thanks

---

## Comment 3

> Username: vricosti  
> Created at: 2021-12-03 10:30:52 UTC  
> Updated at: 2021-12-03 11:42:50 UTC  
> Url: https://github.com/boostorg/describe/issues/20#issuecomment-985405771  

I have modified a bit my struct definitions like this:  
  
```  
struct Item  
   {  
      std::string id;  
      std::string label;  
      std::string iconFile;  
   };  
   BOOST_DESCRIBE_STRUCT(Item, (), (id, label, iconFile))  
     
   using Items = std::vector<Item>;  
     
   struct ItemInfo : public Item  
   {  
      std::string accesLevel;  
      std::string isEnableWhenOffline;  
      std::string isEnableWhenDetectFwUpdate;  
   };  
   BOOST_DESCRIBE_STRUCT(ItemInfo, (Item), (accesLevel, isEnableWhenOffline, isEnableWhenDetectFwUpdate))  
     
   using ItemsInfo = std::vector<ItemInfo>;  
```  
  
and when I serialize in xml my object with the code below:  
  
```  
#include <boost/archive/xml_oarchive.hpp>  
std::ostringstream os;  
boost::archive::xml_oarchive ar(os);  
ar << boost::make_nvp("itemsInfo", itemsInfo);  
std::string s = os.str();  
```  
  
  
I get the following result:  
  
  
```  
<?xml version="1.0" encoding="UTF-8" standalone="yes" ?>  
<!DOCTYPE boost_serialization>  
<boost_serialization signature="serialization::archive" version="19">  
<itemsInfo>  
	<count>1</count>  
	<item_version>0</item_version>  
	<item>  
		<base.1>  
			<id>standalone</id>  
			<label>StandAlone</label>  
			<iconFile>Logo_StandAlone.png</iconFile>  
		</base.1>  
		<accesLevel>CUSTOMER</accesLevel>  
		<isEnableWhenOffline>true</isEnableWhenOffline>  
		<isEnableWhenDetectFwUpdate>true</isEnableWhenDetectFwUpdate>  
	</item>  
</itemsInfo>  
```  
  
Is it possible to not have a node <base.1> and put <id>, <label> <iconfile> at the same level as the other nodes (accessLevel ,...) ?

---

## Comment 4

> Username: pdimov  
> Created at: 2021-12-04 04:53:34 UTC  
> Url: https://github.com/boostorg/describe/issues/20#issuecomment-985967842  

It's possible; you need to change the definition of `serialize` to  
```  
template<class Archive, class T,  
    class D1 = boost::describe::describe_members<T,  
        boost::describe::mod_public | boost::describe::mod_inherited>>  
    void serialize( Archive & ar, T & t, boost::serialization::version_type )  
{  
    boost::mp11::mp_for_each<D1>([&](auto D){  
  
        ar & boost::make_nvp( D.name, t.*D.pointer );  
  
    });  
}  
```  
Using `mod_inherited` enumerates all members, including those from the base classes.

---

## Comment 5

> Username: robertramey  
> Created at: 2021-12-05 03:04:22 UTC  
> Url: https://github.com/boostorg/describe/issues/20#issuecomment-986157644  

> I'm not sure that it would be possible to avoid the <count> due to the architecture of Boost.Serialization, which on reading needs to know how many elements to load. I don't know what's the purpose of <item_version>. It's possible that it's needed for optimized saving and loading of arrays of primitive types, but only Robert can say for sure.  
  
> which on reading needs to know how many elements to load  
  
This is correct.  with out this "useless" element how many elements should one load?  
  
> I don't know what's the purpose of <item_version>  
  
This holds the version number of the element value  type of the vector. Should you change the value of type of the vector, the library will need this it to correctly load the saved vector back into memory.  
  
I think that   
`BOOST_CLASS_IMPLEMENTATION(JsonSerializer::Item, boost::serialization::object_serializable);  
`  
is on the right track - but it seems to that it wouldn't work with xml_archive.  xml_archive serializes through the wrapper BOOST_NVP( ...   So something along the lines of  
alias item = boost::serialization::nap<JsonSerializer::Item>;  
`BOOST_CLASS_IMPLEMENTATION(item, boost::serialization::object_serializable);  
`
