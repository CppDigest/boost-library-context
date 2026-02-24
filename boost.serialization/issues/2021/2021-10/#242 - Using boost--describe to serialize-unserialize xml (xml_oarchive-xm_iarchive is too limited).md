# #242 - Using boost::describe to serialize/unserialize xml (xml_oarchive/xm_iarchive is too limited) [Open]

> Username: vricosti  
> Created at: 2021-10-11 19:05:53 UTC  
> Updated at: 2021-10-12 07:29:04 UTC  
> Url: https://github.com/boostorg/serialization/issues/242  

Hi,  
  
I am using describe to serialize.unserialize some structs to json (thanks to boost::json) but now I would also be able to do it in xml.  
I tried boost::archive::: (xml_oarchive/xml_iarchive) but the resulting xml always have some useless nodes ( <xxx_version>) that cannot be easily removed from what I understand.  
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
Then I try to fill an Items object and I serialize it:  
  
```  
Items items;  
// ... some code to fill items list ...  
std::ostringstream os;  
unsigned int flags = boost::archive::no_header;  
boost::archive::xml_oarchive ar(os, flags);  
ar << boost::make_nvp("items", items.items);  
std::string sXml = os.str();  
```  
  
And the resulting xml looks like this:  
  
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
What can boost offers to serialize/unserialize in xml with boost/describe ?  
It works fine with json so why not with xml ?
