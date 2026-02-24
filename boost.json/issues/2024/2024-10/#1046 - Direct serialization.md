# #1046 - Direct serialization [Closed]

> Username: kelbon  
> Created at: 2024-10-04 13:19:42 UTC  
> Updated at: 2024-12-19 16:06:34 UTC  
> Closed at: 2024-12-16 11:51:52 UTC  
> Url: https://github.com/boostorg/json/issues/1046  

Do library provide interface to serialize structs into json string without creating a json value? As i see, serialization requires json value

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-10-04 13:22:03 UTC  
> Url: https://github.com/boostorg/json/issues/1046#issuecomment-2393697873  

Direct serialization will be released with Boost 1.87.

---

## Comment 2

> Username: kelbon  
> Created at: 2024-10-04 13:23:10 UTC  
> Url: https://github.com/boostorg/json/issues/1046#issuecomment-2393700961  

what interface it will provide?

---

## Comment 3

> Username: grisumbras  
> Created at: 2024-10-04 13:27:31 UTC  
> Url: https://github.com/boostorg/json/issues/1046#issuecomment-2393709302  

Basically the same as with `value` serialization.  
https://www.boost.org/doc/libs/develop/libs/json/doc/html/json/ref/boost__json__serialize/overload5.html

---

## Comment 4

> Username: kelbon  
> Created at: 2024-10-04 13:29:36 UTC  
> Url: https://github.com/boostorg/json/issues/1046#issuecomment-2393713320  

So it will work with boost pfr/ boost describe struct or what? I need more low lower level interface with serializing strings/ints/keys etc

---

## Comment 5

> Username: kelbon  
> Created at: 2024-10-04 13:33:55 UTC  
> Url: https://github.com/boostorg/json/issues/1046#issuecomment-2393721857  

Such interface witih returning string will not match with output iterators, (for not copying string after it created), also i think such overload serialize(auto& value, options) may break many overloads in current code

---

## Comment 6

> Username: grisumbras  
> Created at: 2024-10-04 14:01:04 UTC  
> Url: https://github.com/boostorg/json/issues/1046#issuecomment-2393783076  

I can assure you that we have tests for this, no overload ambiguity happens.  
  
If you want something more granular, `setializer` also will support direct operation.  
https://www.boost.org/doc/libs/develop/libs/json/doc/html/json/ref/boost__json__serializer/reset/overload5.html

---

## Comment 7

> Username: grisumbras  
> Created at: 2024-12-16 11:51:52 UTC  
> Url: https://github.com/boostorg/json/issues/1046#issuecomment-2545416525  

Now that 1.87.0 is out, this issue is resolved.

---

## Comment 8

> Username: kelbon  
> Created at: 2024-12-16 12:26:18 UTC  
> Url: https://github.com/boostorg/json/issues/1046#issuecomment-2545495524  

i dont think it what i expect from direct serializing, i was about sax interface for writing string/integer/double etc similar to direct parsing interface

---

## Comment 9

> Username: grisumbras  
> Created at: 2024-12-17 16:24:35 UTC  
> Url: https://github.com/boostorg/json/issues/1046#issuecomment-2548941013  

Direct parsing doesn't provide a SAX interface in this library. It's the other way around: direct parsing is implemented on top of a SAX interface.  
  
That being said, what do you need event-based serialisation for?

---

## Comment 10

> Username: kelbon  
> Created at: 2024-12-17 16:53:44 UTC  
> Url: https://github.com/boostorg/json/issues/1046#issuecomment-2549023071  

> Direct parsing doesn't provide a SAX interface in this library. It's the other way around: direct parsing is implemented on top of a SAX interface.  
>   
> That being said, what do you need event-based serialisation for?  
  
to not create json object for serialization of my struct

---

## Comment 11

> Username: grisumbras  
> Created at: 2024-12-17 17:51:35 UTC  
> Url: https://github.com/boostorg/json/issues/1046#issuecomment-2549187740  

But this is exactly what direct serialisation achieves. See these:  
https://www.boost.org/doc/libs/1_87_0/libs/json/doc/html/json/ref/boost__json__serializer/reset/overload5.html  
https://www.boost.org/doc/libs/1_87_0/libs/json/doc/html/json/ref/boost__json__serialize/overload5.html

---

## Comment 12

> Username: kelbon  
> Created at: 2024-12-17 18:29:30 UTC  
> Url: https://github.com/boostorg/json/issues/1046#issuecomment-2549282585  

> But this is exactly what direct serialisation achieves. See these: https://www.boost.org/doc/libs/1_87_0/libs/json/doc/html/json/ref/boost__json__serializer/reset/overload5.html https://www.boost.org/doc/libs/1_87_0/libs/json/doc/html/json/ref/boost__json__serialize/overload5.html  
  
how i can use it for my type?

---

## Comment 13

> Username: grisumbras  
> Created at: 2024-12-18 11:37:31 UTC  
> Url: https://github.com/boostorg/json/issues/1046#issuecomment-2551092716  

There appears to be some miscommunication. My first inclination was to answer with "by calling one of these functions with an object of your type". But that should be obvious. Am I missing something? Is there something special about your types that exclude them from being used with these functions?

---

## Comment 14

> Username: kelbon  
> Created at: 2024-12-18 11:50:49 UTC  
> Url: https://github.com/boostorg/json/issues/1046#issuecomment-2551122631  

> There appears to be some miscommunication. My first inclination was to answer with "by calling one of these functions with an object of your type". But that should be obvious. Am I missing something? Is there something special about your types that exclude them from being used with these functions?  
  
For example, if i have such type and want to serialize it like json array of 'count' of 'hello world' strings, how to do it with such interface effectively?  
  
```cpp  
struct my_type {  
int count;  
  
my_type(int c) : count(c) {}  
  
};  
  
```

---

## Comment 15

> Username: grisumbras  
> Created at: 2024-12-18 11:58:32 UTC  
> Url: https://github.com/boostorg/json/issues/1046#issuecomment-2551137828  

Oh, this is currently not supported by either direct parsing or direct serialization. But I'm working on a feature that I think would allows such representation.

---

## Comment 16

> Username: grisumbras  
> Created at: 2024-12-18 11:59:10 UTC  
> Url: https://github.com/boostorg/json/issues/1046#issuecomment-2551138981  

See #1025

---

## Comment 17

> Username: kelbon  
> Created at: 2024-12-18 12:05:59 UTC  
> Updated at: 2024-12-18 12:06:07 UTC  
> Url: https://github.com/boostorg/json/issues/1046#issuecomment-2551151761  

Now i using rapidjson for this functionality:  
https://rapidjson.org/classrapidjson_1_1_writer.html  
  
Its usefull, convenient and expandable, also seems to be easy to implement (more easy then json parsing etc)

---

## Comment 18

> Username: jcmonnin  
> Created at: 2024-12-18 17:10:06 UTC  
> Updated at: 2024-12-18 17:11:20 UTC  
> Url: https://github.com/boostorg/json/issues/1046#issuecomment-2551860747  

I also would welcome a SAX interface to ease porting a large existing codebase from RapidJSON to Boost JSON. When writing JSON, the SAX interface it pretty convenient if only the serialized JSON string is of interest, and it's very performant (as it avoids the extra copy of data to JSON value data structures).  
  
https://rapidjson.org/md_doc_sax.html#Writer  
  
In my short test replacing RapidJSON SAX writing code with standard Boost JSON code would mean a big performance regression (can share benchmark code if interested). Due to vastly different API it would also mean a lot of work. Similar SAX interface would ease porting a lot (more important point than performance).  
  
That being said, I don't understand direct serializing provided by 1.87.0, and comment above is without taking into consideration direct serialization. Does it avoid the copy, or does it call `boost::json::value_from` behind the scenes? Where is the documentation/example of how to use direct serialization (besides reference documentation)?  
  
How would I implement following function using direct serialization?  
`std::string generatJSON(const std::vector<std::string>& columnNames, const std::vector<std::vector<double>>& rows)`  
  
where I want following resulting JSON:  
```  
{  
  ColumnNames : ["Bla", "Bla],  
  Rows : [  
    [1,2],  
    [3,4],  
  ],  
}  
```

---

## Comment 19

> Username: grisumbras  
> Created at: 2024-12-18 18:16:55 UTC  
> Url: https://github.com/boostorg/json/issues/1046#issuecomment-2551983210  

Direct operations are described here: https://www.boost.org/doc/libs/1_87_0/libs/json/doc/html/json/conversion/direct_parsing.html. Seems like I forgot to rename the section.  
  
Serialising two objects as if they were one is not supported, you'd need something like this:  
  
  
```c++  
#include <boost/describe/class.hpp>  
#include <boost/core/span.hpp>  
  
struct rows_and_cols  
{  
    boost::span<std::string const> ColumnNames;  
    boost::span<std::vector<double> const> Rows;  
};  
BOOST_DESCRIBE_STRUCT( rows_and_cols, (), (ColumnNames, Rows) );  
  
std::string generatJSON(const std::vector<std::string>& columnNames, const std::vector<std::vector<double>>& rows)  
{  
  rows_and_cols data{columnNames, rows};  
  return boost::json::serialize(data);  
}  
```

---

## Comment 20

> Username: jcmonnin  
> Created at: 2024-12-19 10:41:33 UTC  
> Updated at: 2024-12-19 10:43:56 UTC  
> Url: https://github.com/boostorg/json/issues/1046#issuecomment-2553385917  

Thanks for the example, which allows me to compare performance in my benchmark (serializing a 82MB document with structure as above; the output contains mostly number, which is the bottleneck in our server).  
  
- RapidJSON SAX: 648ms  
- RapidJSON DOM: Build Value 289ms + Seralize 723ms = 1012ms  
- Boost JSON: Build Value 510ms + Serialize 511ms = 1021ms  
- Boost JSON Direct Serialization: 485ms  
  
Nice to see that the performance regression of porting to Boost JSON would the solved with direct serialization. There is even some nice performance gains. (As always benchmarks are highly case specific, different use cases might give difference results).  
  
The API issue remains however. It seems direct serialization relies on boost describe reflections. Besides porting being a lot of work due to different API style, there are also things that I would have trouble to implement (like omitting object properties that are at default values to save bandwidth, generating different versions of the JSON structure depending on client's request, ...). Use case is generating JSON on server, where JSON needs to follow some spec and memory layout of C++ structures are only loosely coupled to required JSON format. I don't find the code with helper structs to be very readable compare to SAX, and certainly less flexible.  
  
> Direct parsing doesn't provide a SAX interface in this library. It's the other way around: direct parsing is implemented on top of a SAX interface.  
>  
> That being said, what do you need event-based serialisation for?  
  
As the RapidJSON SAX API is pretty trivial (a dozen of simple to use methods in a writer class) I had a quick looks if it would be easily doable using your writer class, but it looks like the writer is fairly low level to be used externally (besides being a non-public interface). Is there any chance you would consider a RapidJSON-style SAX interface for your writer class?

---

## Comment 21

> Username: grisumbras  
> Created at: 2024-12-19 15:37:36 UTC  
> Url: https://github.com/boostorg/json/issues/1046#issuecomment-2554673920  

I don't think it would be possible to retrofit such interface to our serializer. The issue is that it would require complete reversal of its workflow. Moreover, such push-based approach doesn't work well with chunked output, which is central to our implementation.  
  
That being said, every subvalue of JSON is also a full JSON value, so you could do  
  
```c++  
std::string generatJSON(const std::vector<std::string>& columnNames, const std::vector<std::vector<double>>& rows)  
{  
  std::string result = "{";  
    
  result += json::serialize( json::string_view("ColumnNames") );  
  result += ":";   
  result += json::serialize(columnNames);  
    
  result += ",";  
  result += json::serialize( json::string_view("Rows") );  
  result += ":";   
  result += json::serialize(rows);  
    
  result += "}";  
  return result;  
}  
```  
  
If that appears too slow (due to a lot of temporary strings), you can try using serializer directly:  
```c++  
void  
serialize_helper(std::string& s, json::serializer& sr)  
{  
    char buf[1024];  
    string_view sv;  
    do  
    {  
        sv = sr.read(buf);  
        s.append( sv.data(), sv.size() );  
    } while( !sr.done() );  
}  
  
template< class T >  
void  
serialize(std::string& s, json::serializer& sr, T const& t)  
{  
    sr.reset( std::addressof(t) );  
    serialize_helper(s, sr);  
}  
  
std::string generatJSON(const std::vector<std::string>& columnNames, const std::vector<std::vector<double>>& rows)  
{  
    unsigned char buf[256];  
    json::serializer sr( {}, buf, sizeof(buf) );  
  
    std::string result = "{";  
  
    serialize( result, sr, json::string_view("ColumnNames") );  
    result += ":";  
    serialize( result, sr, columnNames);  
  
    result += ",";  
    serialize( result, sr, json::string_view("Rows") );  
    result += ":";  
    serialize( result, sr, rows);  
  
    result += "}";  
    return result;  
}  
```  
  
`serialize_helper` can be further optimized if needed to do less copying.

---

## Comment 22

> Username: kelbon  
> Created at: 2024-12-19 16:06:32 UTC  
> Url: https://github.com/boostorg/json/issues/1046#issuecomment-2554826107  

> I don't think it would be possible to retrofit such interface to our serializer. The issue is that it would require complete reversal of its workflow. Moreover, such push-based approach doesn't work well with chunked output, which is central to our implementation.  
>   
> That being said, every subvalue of JSON is also a full JSON value, so you could do  
>   
> std::string generatJSON(const std::vector<std::string>& columnNames, const std::vector<std::vector<double>>& rows)  
> {  
>   std::string result = "{";  
>     
>   result += json::serialize( json::string_view("ColumnNames") );  
>   result += ":";   
>   result += json::serialize(columnNames);  
>     
>   result += ",";  
>   result += json::serialize( json::string_view("Rows") );  
>   result += ":";   
>   result += json::serialize(rows);  
>     
>   result += "}";  
>   return result;  
> }  
> If that appears too slow (due to a lot of temporary strings), you can try using serializer directly:  
>   
> void  
> serialize_helper(std::string& s, json::serializer& sr)  
> {  
>     char buf[1024];  
>     string_view sv;  
>     do  
>     {  
>         sv = sr.read(buf);  
>         s.append( sv.data(), sv.size() );  
>     } while( !sr.done() );  
> }  
>   
> template< class T >  
> void  
> serialize(std::string& s, json::serializer& sr, T const& t)  
> {  
>     sr.reset( std::addressof(t) );  
>     serialize_helper(s, sr);  
> }  
>   
> std::string generatJSON(const std::vector<std::string>& columnNames, const std::vector<std::vector<double>>& rows)  
> {  
>     unsigned char buf[256];  
>     json::serializer sr( {}, buf, sizeof(buf) );  
>   
>     std::string result = "{";  
>   
>     serialize( result, sr, json::string_view("ColumnNames") );  
>     result += ":";  
>     serialize( result, sr, columnNames);  
>   
>     result += ",";  
>     serialize( result, sr, json::string_view("Rows") );  
>     result += ":";  
>     serialize( result, sr, rows);  
>   
>     result += "}";  
>     return result;  
> }  
> `serialize_helper` can be further optimized if needed to do less copying.  
  
Firstly, you can use output iterator (or buffer for more efficiently) instead of generating std::string every time.  
  
And you can make serializing streamable by using coroutines.  
  
For example here i use our boost direct parsing with coroutines, which outperforms rapidjson and your direct parsing implementation in my benchmark:  
  
https://github.com/bot-motherlib/TGBM/blob/master/include/tgbm/jsons/boostjson_sax_producer.hpp
