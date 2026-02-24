# #43 - Stack-overflow with deep XML in boost::property_tree::xml_parser::read_xml_node [Open]

> Username: ForodrenDraug  
> Created at: 2019-09-25 15:05:44 UTC  
> Updated at: 2022-03-03 09:09:23 UTC  
> Url: https://github.com/boostorg/property_tree/issues/43  

I'm using the property_tree to parse an XML with  
```  
std::wstringstream responseStream(response);  
boost::property_tree::wptree responseTree;  
boost::property_tree::read_xml(responseStream, responseTree);  
```  
and the stack-overflow error occurs in void boost::property_tree::xml_parser::read_xml_node when trying to parse considerably deep XMLs.  
  
AddressSanitizer's trace:  
[boost_property_tree_stack_overflow_AddressSanitizer.txt](https://github.com/boostorg/property_tree/files/3653037/boost_property_tree_stack_overflow_AddressSanitizer.txt)  
  
Boost version: 1.66.0

---

## Comment 1

> Username: hdu-sdlzx  
> Created at: 2021-09-29 03:43:51 UTC  
> Updated at: 2021-09-29 03:46:15 UTC  
> Url: https://github.com/boostorg/property_tree/issues/43#issuecomment-929804740  

Similar problems with json and info, do we have plans to fix them or just use ulimit to workaround? @pdimov @madmongo1

---

## Comment 2

> Username: madmongo1  
> Created at: 2021-09-29 07:08:08 UTC  
> Url: https://github.com/boostorg/property_tree/issues/43#issuecomment-929898113  

Thanks for bringing this to my attention - I see the issue dates from before my involvement.  
Any chance you can put together a minimal test to replicate? I appreciate that stack depth is a host-dependenct thing.  
  
Can you share details about the environment this happens in? It'll be useful to know what I'm working with.

---

## Comment 3

> Username: hdu-sdlzx  
> Created at: 2021-09-30 07:56:34 UTC  
> Updated at: 2021-09-30 07:56:54 UTC  
> Url: https://github.com/boostorg/property_tree/issues/43#issuecomment-930968268  

A minimal test is here @madmongo1 :  
  
#include <sstream>  
using namespace std;  
#include <boost/property_tree/xml_parser.hpp>  
using namespace boost::property_tree;  
  
int main()  
{  
        stringstream ss;  
        for (int i = 0; i < 100000; ++i)  
                ss << "<z>";  
        for (int i = 0; i < 100000; ++i)  
                ss << "</z>";  
        ptree tree;  
        read_xml(ss, tree, 0);  
        return 0;  
}  
  
We found this problem when running oss-fuzz for boost, and we are using linux with a default stack size of 8KB. If we set stack size to unlimited, we can pass this test case.  
We know it's a limit of linux, and we want to know if there will be any plan to refactor read_xml (and similar functions for json/info format) and use a non-recursive implementation to avoid the problem?

---

## Comment 4

> Username: hdu-sdlzx  
> Created at: 2021-10-14 14:32:59 UTC  
> Url: https://github.com/boostorg/property_tree/issues/43#issuecomment-943417838  

ping @madmongo1

---

## Comment 5

> Username: hdu-sdlzx  
> Created at: 2022-03-03 02:10:10 UTC  
> Url: https://github.com/boostorg/property_tree/issues/43#issuecomment-1057589024  

ping @madmongo1

---

## Comment 6

> Username: madmongo1  
> Created at: 2022-03-03 09:09:23 UTC  
> Url: https://github.com/boostorg/property_tree/issues/43#issuecomment-1057829470  

Thank you.
