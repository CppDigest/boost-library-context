# #887 - compile error on value_to a map when includes boost/range.hpp [Closed]

> Username: shuizhongyi  
> Created at: 2023-05-17 07:32:12 UTC  
> Updated at: 2023-05-17 19:12:03 UTC  
> Closed at: 2023-05-17 19:12:03 UTC  
> Url: https://github.com/boostorg/json/issues/887  

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
> Created at: 2023-05-17 07:47:49 UTC  
> Url: https://github.com/boostorg/json/issues/887#issuecomment-1550918598  

Yeah, it's a bug in JSON. Thanks for finding it!

---

## Comment 2

> Username: grisumbras  
> Created at: 2023-05-17 07:51:30 UTC  
> Url: https://github.com/boostorg/json/issues/887#issuecomment-1550923308  

To be honest, I'm afraid there's no way to not trigger that bug. You would have to wait for a new release, probably.

---

## Comment 3

> Username: grisumbras  
> Created at: 2023-05-17 07:54:37 UTC  
> Url: https://github.com/boostorg/json/issues/887#issuecomment-1550927572  

If you're OK with patching your local JSON sources, replace  
  
```c++  
template<class T>  
std::insert_iterator<T>  
inserter(  
    T& target,  
    mp11::mp_int<0>)  
{  
    return std::inserter(target, end(target));  
}  
```  
  
with  
```c++  
template<class T>  
std::insert_iterator<T>  
inserter(  
    T& target,  
    mp11::mp_int<0>)  
{  
    return std::inserter( target, target.end() );  
}  
```  
in `boost/json/detail/value_to.hpp`

---

## Comment 4

> Username: shuizhongyi  
> Created at: 2023-05-17 11:08:56 UTC  
> Url: https://github.com/boostorg/json/issues/887#issuecomment-1551194471  

@grisumbras   
Got it, thank you very much.

---

## Comment 5

> Username: grisumbras  
> Created at: 2023-05-17 18:52:47 UTC  
> Url: https://github.com/boostorg/json/issues/887#issuecomment-1551897930  

There's actually another suggestion: move `#include "boost/range.hpp"` below `#include "boost/json.hpp"`. It's rather brittle, but it will make the code compile.
