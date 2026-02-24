# #962 - Exception of segment fail when run value_from for tree structuor [Closed]

> Username: akeyliu  
> Created at: 2023-12-20 12:06:52 UTC  
> Updated at: 2023-12-25 09:05:30 UTC  
> Closed at: 2023-12-25 09:05:30 UTC  
> Url: https://github.com/boostorg/json/issues/962  

PLEASE DON'T FORGET TO "STAR" THIS REPOSITORY :)  
  
When reporting a bug please include the following:  
  
### Version of Boost 1.81  
  
You can find the version number in the file `<boost/version.hpp>`  
  
### Steps necessary to reproduce the problem  
1. In source code define structure of Tree for Deep First Search Algorithem;   
2. Sourc code like this:   
` structure CRouteData; `  
` structure CRouteData { `  
`  int iNoUpdateCount; `  
` list<CRouteData> listRouteDataSon;`  
`};`  
3. Define value_from and value_to for structure CRouteData;  
4.  Return segmentation fail when running value_from.  
I think the root reason is the iterative call of CRouteData;   
  
A small compiling program is the best. If your code is  
public, you can provide a link to the repository.  
  
### All relevant compiler information g++ 12.2.0 Debian 12  
  
If you are unable to compile please include the type and  
version of compiler you are using as well as all compiler  
output including the error message, file, and line numbers  
involved.  
  
The more information you provide the sooner your issue  
can get resolved!

---

## Comment 1

> Username: grisumbras  
> Created at: 2023-12-20 12:55:57 UTC  
> Url: https://github.com/boostorg/json/issues/962#issuecomment-1864428272  

Given the information you've given me, I can't reproduce it. You should definitely at least provide your implementation of `tag_invoke(value_from_tag)`. Even better if you provide a minimal reproduceable example, that is something I could just copy into an empty file and get your error.

---

## Comment 2

> Username: akeyliu  
> Created at: 2023-12-25 09:05:17 UTC  
> Url: https://github.com/boostorg/json/issues/962#issuecomment-1868872529  

I develop test source code and it's work.  I need to check other source code. Sorry to bother you!   
The test source code is like follow. I try to use reflect like macro function to automatically generate the value_from and value_to function for specific class/structor.    
`  
#include <boost/json/src.hpp>  
#include <list>  
#include <iostream>  
#include <type_traits>  
  
using namespace std;  
using namespace boost;  
  
struct CRouteData;  
struct CRouteData {  
    int iCount;  
    list<CRouteData> listSonRouteData;  
  
    friend void tag_invoke( const json::value_from_tag&, json::value &_j, CRouteData const &_t) {  
        json::object _obj;  
        _obj.emplace("iCount", json::value_from(_t.iCount));  
        _obj.emplace("listSonRouteData", json::value_from(_t.listSonRouteData));  
        _j.emplace_object() = _obj;  
    };  
    friend CRouteData tag_invoke( const json::value_to_tag<CRouteData>&, json::value const &_j ) {  
        CRouteData _t;  
        _t.iCount = json::value_to<decltype(_t.iCount)>(_j.at("iCount"));  
        _t.listSonRouteData = json::value_to<decltype(_t.listSonRouteData)>(_j.at("listSonRouteData"));  
        return _t;  
    };  
};  
  
int main(void) {  
    CRouteData objRouteData{0, {}}, objRouteDataSon1{2, {}}, objRouteDataSon2{3, {}};  
    list<CRouteData> listData{objRouteData}, *ptrList = &listData;  
    for( auto iDepth = 0; iDepth < 100 && !ptrList->empty(); iDepth++ ) {  
        if( !ptrList->empty() )  
            ptrList = &(ptrList->back().listSonRouteData);  
        for( auto i = 0; i <= iDepth; i++) {  
            CRouteData objRouteDataNow{i, {}};  
            ptrList->emplace_back(objRouteDataNow);  
        }  
        cout << "iDepth:" << iDepth << " [" << json::value_from(ptrList->back()) << "] Total:" << json::value_from(listData) << endl;  
    }  
    return 0;  
}  
`
