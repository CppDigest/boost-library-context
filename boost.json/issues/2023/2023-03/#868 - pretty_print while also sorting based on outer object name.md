# #868 - pretty_print while also sorting based on outer object name [Closed]

> Username: TryerGit  
> Created at: 2023-03-07 17:10:57 UTC  
> Updated at: 2023-03-08 17:14:14 UTC  
> Closed at: 2023-03-08 13:02:56 UTC  
> Url: https://github.com/boostorg/json/issues/868  

PLEASE DON'T FORGET TO "STAR" THIS REPOSITORY :)  
  
When reporting a bug please include the following:  
  
### Version of Boost  
  
1.81.0  
  
You can find the version number in the file `<boost/version.hpp>`  
  
### Steps necessary to reproduce the problem  
  
The pretty print example https://www.boost.org/doc/libs/1_75_0/libs/json/doc/html/json/examples.html would work.  
  
### All relevant compiler information  
  
MSVC 14.2 also g++ on ubuntu.  
  
----  
  
This is not a bug report. It is just a query about whether there is a way to have pretty_print also sort (ascending or descending) its output based on the outermost object name?  
  
I am aware that there are separate command line tools that act on a pre-existing json file and produce an output that is sorted. E.g., `jq`. I had raised a query regarding exactly this on SO [here](https://stackoverflow.com/questions/75450348/sorting-a-json-file-by-outer-object-name) for which the solution was to use `jq`.  
  
I'd rather not want to run `jq` separately -- is it possible to enhance pretty_print to make such modifications on the fly within boost::json itself?  
  
Thank you.

---

## Comment 1

> Username: pdimov  
> Created at: 2023-03-07 18:25:58 UTC  
> Url: https://github.com/boostorg/json/issues/868#issuecomment-1458632207  

You need to extract the keys from the `json::object` first, sort them, then output the values using the sorted keys. Like this: https://godbolt.org/z/TEe1d3Y4Y

---

## Comment 2

> Username: TryerGit  
> Created at: 2023-03-08 12:09:17 UTC  
> Updated at: 2023-03-08 12:10:17 UTC  
> Url: https://github.com/boostorg/json/issues/868#issuecomment-1460061457  

> You need to extract the keys from the `json::object` first, sort them, then output the values using the sorted keys. Like this: https://godbolt.org/z/TEe1d3Y4Y  
  
Thank you.  
  
In trying to replicate this at my end, I have the following Isource8.json file with unprettified raw content:  
  
`{"1":{"height":120,"metrics":[[-3,-2,-1],[0,1,2,3]]},"2":{"height":130,"metrics":[[-3,-2,-1,-4],[0,1],[5,7]]}}`  
  
Then, the following code reads this content and default pretty_prints into an Osource8-1.json file based on the pretty_print function available at : https://www.boost.org/doc/libs/1_75_0/libs/json/doc/html/json/examples.html  
  
Then, the code pretty_prints into an Osource8-2.json file based on the pretty_print function you have provided at Godbolt and sorts it in descending order.  
  
Everything works fine as expected except that in the latter case, there is an additional unnecessary `,` after the last entry pertaining to key "1". This may be okay from a JSON format point of view. However, my text editor (VSCode) highlights this as a squiggly red error (Trailing comma json 519, which could be a VSCode internal error number) and I am unsure if this will break something else down the line.  
  
The entire code is:  
  
```cpp  
#include <boost/json.hpp>  
using namespace boost::json;  
#include <boost/filesystem.hpp>  
using namespace boost::filesystem;  
#include <vector>  
#include <iostream>  
#include <fstream>  
  
void pretty_print(std::ostream& os, boost::json::value const& jv, std::string* indent = nullptr)  
{  
    std::string indent_;  
    if (!indent)  
        indent = &indent_;  
    switch (jv.kind())  
    {  
    case boost::json::kind::object:  
    {  
        os << "{\n";  
        indent->append(4, ' ');  
        auto const& obj = jv.get_object();  
        if (!obj.empty())  
        {  
            auto it = obj.begin();  
            for (;;)  
            {  
                os << *indent << boost::json::serialize(it->key()) << " : ";  
                pretty_print(os, it->value(), indent);  
                if (++it == obj.end())  
                    break;  
                os << ",\n";  
            }  
        }  
        os << "\n";  
        indent->resize(indent->size() - 4);  
        os << *indent << "}";  
        break;  
    }  
  
    case boost::json::kind::array:  
    {  
        os << "[\n";  
        indent->append(4, ' ');  
        auto const& arr = jv.get_array();  
        if (!arr.empty())  
        {  
            auto it = arr.begin();  
            for (;;)  
            {  
                os << *indent;  
                pretty_print(os, *it, indent);  
                if (++it == arr.end())  
                    break;  
                os << ",\n";  
            }  
        }  
        os << "\n";  
        indent->resize(indent->size() - 4);  
        os << *indent << "]";  
        break;  
    }  
  
    case boost::json::kind::string:  
    {  
        os << boost::json::serialize(jv.get_string());  
        break;  
    }  
  
    case boost::json::kind::uint64:  
        os << jv.get_uint64();  
        break;  
  
    case boost::json::kind::int64:  
        os << jv.get_int64();  
        break;  
  
    case boost::json::kind::double_:  
        os << jv.get_double();  
        break;  
  
    case boost::json::kind::bool_:  
        if (jv.get_bool())  
            os << "true";  
        else  
            os << "false";  
        break;  
  
    case boost::json::kind::null:  
        os << "null";  
        break;  
    }  
  
    if (indent->empty())  
        os << "\n";  
}  
  
void pretty_print_new(std::ostream& os, boost::json::value const& jv, std::string* indent = nullptr)  
{  
    std::string indent_;  
    if (!indent)  
        indent = &indent_;  
  
    switch (jv.kind())  
    {  
    case boost::json::kind::object:  
    {  
        os << "{\n";  
        indent->append(4, ' ');  
  
        std::vector<boost::json::string> keys;  
  
        for (auto const& v : jv.get_object())  
        {  
            keys.push_back(v.key());  
        }  
  
        std::sort(keys.begin(), keys.end(), std::greater<>());  
  
        for (auto const& k : keys)  
        {  
            os << *indent << boost::json::serialize(k) << ": ";  
            pretty_print(os, jv.at(k), indent);  
            os << ",\n";  
        }  
  
        indent->resize(indent->size() - 4);  
        os << *indent << "}";  
        break;  
    }  
  
    case boost::json::kind::array:  
    {  
        os << "[\n";  
        indent->append(4, ' ');  
  
        for (auto const& v : jv.get_array())  
        {  
            os << *indent;  
            pretty_print(os, v, indent);  
            os << ",\n";  
        }  
  
        indent->resize(indent->size() - 4);  
        os << *indent << "]";  
        break;  
    }  
  
    default:  
    {  
        os << boost::json::serialize(jv);  
        break;  
    }  
    }  
  
    if (indent->empty())  
        os << "\n";  
}  
  
  
int main() {  
	std::string fname = "Isource8.json";  
	//Read JSON file and store in json::object  
        const path fileName(fname);  
	object obj;  
	std::ifstream file(fname);  
	std::string content(std::istreambuf_iterator<char>{file}, std::istreambuf_iterator<char>{});  
	value jv = parse(content);  
	obj = jv.as_object();  
	file.close();  
          
        //Pretty print (original source code) to OSource8-1.json  
        fname = "Osource8-1.json";  
        std::ofstream ofile1(fname);  
        pretty_print(ofile1, obj);  
        ofile1.close();  
  
        //Pretty print (New source code) to OSource8-2.json  
        fname = "Osource8-2.json";  
        std::ofstream ofile2(fname);  
        pretty_print_new(ofile2, obj);//Sorts in descending order  
        ofile2.close();//There is an unnecessary extra , after "1"  
}  
```  
  
(apologies for the wall of code text here. I am trying to figure out how to load Isource8.json over at GodBolt and trying to see if it can output Osource8-1.json and Osource8-2.json in a Godbolt project on the website itself.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2023-03-08 12:14:49 UTC  
> Updated at: 2023-03-08 12:15:51 UTC  
> Url: https://github.com/boostorg/json/issues/868#issuecomment-1460067394  

As a matter of principle you should want to get rid of that extra comma :)  
  
It is not standard JSON. Our parser accepts it only when you set an option to relax conformance:  
https://www.boost.org/doc/libs/1_81_0/libs/json/doc/html/json/ref/boost__json__parse_options/allow_trailing_commas.html

---

## Comment 4

> Username: vinniefalco  
> Created at: 2023-03-08 12:18:00 UTC  
> Updated at: 2023-03-08 12:18:30 UTC  
> Url: https://github.com/boostorg/json/issues/868#issuecomment-1460070923  

Instead of outputting the comma _after_ each element and special-casing the last comma, output the comma _before_ each element but the first. So conceptually, instead of this:  
  
1,  
2,  
3,  
4,  
  
output this instead:  
  
,1  
,2  
,3  
,4  
  
It is easier to unroll the first iteration through the loop to leave out the first comma than it is to detect when you are at the last loop iteration (and faster too), leaving you with this:  
  
1  
,2  
,3  
,4

---

## Comment 5

> Username: TryerGit  
> Created at: 2023-03-08 12:42:59 UTC  
> Url: https://github.com/boostorg/json/issues/868#issuecomment-1460097864  

> Instead of outputting the comma _after_ each element and special-casing the last comma, output the comma _before_ each element but the first. So conceptually, instead of this:  
>   
> 1, 2, 3, 4,  
>   
> output this instead:  
>   
> ,1 ,2 ,3 ,4  
>   
> It is easier to unroll the first iteration through the loop to leave out the first comma than it is to detect when you are at the last loop iteration (and faster too), leaving you with this:  
>   
> 1 ,2 ,3 ,4  
  
Yes, I was able to make a change along the lines you suggested and it works fine. The change was   
  
```cpp  
        int nokeys = static_cast<int>(keys.size());  
  
        int count = 0;  
        for (auto const& k : keys)  
        {  
            os << *indent << boost::json::serialize(k) << ": ";  
            pretty_print(os, jv.at(k), indent);  
            if(count < nokeys - 1)  
                os << ",\n";  
            else  
                os << "\n";  
            count++;  
        }  
```  
  
Possibly not the most elegant way to code this, but it seems easier for me to maintain and understand what is going on. I run this part of my code only once and hence speed is not of the utmost importance -- although I understand your idea of unrolling the first iteration and then loop through the remaining.

---

## Comment 6

> Username: pdimov  
> Created at: 2023-03-08 17:14:14 UTC  
> Url: https://github.com/boostorg/json/issues/868#issuecomment-1460523704  

> As a matter of principle you should want to get rid of that extra comma :)  
  
As a matter of principle, https://godbolt.org/z/44nYjnjeY
