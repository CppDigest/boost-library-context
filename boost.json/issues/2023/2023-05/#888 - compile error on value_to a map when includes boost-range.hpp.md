# #888 - compile error on value_to a map when includes boost/range.hpp [Closed]

> Username: shuizhongyi  
> Created at: 2023-05-17 07:55:49 UTC  
> Updated at: 2023-05-17 08:52:19 UTC  
> Closed at: 2023-05-17 08:52:19 UTC  
> Url: https://github.com/boostorg/json/issues/888  

### Version of Boost  
BOOST_VERSION 108100  
  
### Steps necessary to reproduce the problem  
The demo code looks like this:  
  
#include "boost/range.hpp"  
#include "boost/json.hpp"  
  
int main()  
{  
    std::string json_string{}  
    auto jv = boost::json::parse(json_string);  
    boost::json::value_to<std::map<std::string, std::string>>(jv);  
}  
  
and this will cause compile error:  
boost\json\detail\value_to.hpp(120,1): error C2668: 'boost::range_adl_barrier::end': ambiguous call to overloaded function  
boost\range\end.hpp(92,55): message : could be 'std::_Tree_iterator<std::_Tree_val<std::_Tree_simple_types<std::pair<const std::string,std::string>>>> boost::range_adl_barrier::end<T>(T &)'  
MSVC\14.29.30133\include\xutility(1854,30): message : or       'std::_Tree_iterator<std::_Tree_val<std::_Tree_simple_types<std::pair<const std::string,std::string>>>> std::end<T>(_Container &)'  
...  
boost\json\detail\value_to.hpp(120,17): error C2672: 'std::inserter': no matching overloaded function found  
  
If remove #include "boost/range.hpp", compile successfully.  
  
so, is there any misusage in here, or any way to avoid this compile error??  
  
Thanks!

---

## Comment 1

> Username: grisumbras  
> Created at: 2023-05-17 08:51:58 UTC  
> Url: https://github.com/boostorg/json/issues/888#issuecomment-1551005867  

Duplicate of #887
