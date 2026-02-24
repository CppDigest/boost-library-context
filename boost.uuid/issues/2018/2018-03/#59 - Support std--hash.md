# #59 - Support std::hash [Closed]

> Username: krojew  
> Created at: 2018-03-13 07:56:45 UTC  
> Updated at: 2018-05-07 11:54:32 UTC  
> Closed at: 2018-05-07 11:54:32 UTC  
> Url: https://github.com/boostorg/uuid/issues/59  

boost::uuids::uuid now supports only boost:hash via hash_value(), making it an unnecessary dependency on platforms supporting >= c++11. There also should be a std::hash specialization.

---

## Comment 1

> Username: jeking3  
> Created at: 2018-03-13 14:50:19 UTC  
> Url: https://github.com/boostorg/uuid/issues/59#issuecomment-372692251  

Hi, thanks for reporting the issue.  Can you add any more details or examples?

---

## Comment 2

> Username: krojew  
> Created at: 2018-03-13 15:46:40 UTC  
> Url: https://github.com/boostorg/uuid/issues/59#issuecomment-372712729  

A typical usage example would be using uuid as the key for std::unordered_map. Since uuid already supports boost::hash, it would be nice to also support std::hash, to make that example work without changing the default hasher. In other words, what's missing is a std::hash specialization for uuid.

---

## Comment 3

> Username: jeking3  
> Created at: 2018-03-26 18:43:19 UTC  
> Url: https://github.com/boostorg/uuid/issues/59#issuecomment-376270266  

If you can provide a simple code example of this that I could build into a unit test for C++11 or later, I would appreciate it.

---

## Comment 4

> Username: krojew  
> Created at: 2018-03-26 18:58:13 UTC  
> Updated at: 2018-03-28 07:20:26 UTC  
> Url: https://github.com/boostorg/uuid/issues/59#issuecomment-376274817  

A simple example can be:  
  
```  
#include <unordered_set>  
#include <boost/uuid.hpp>  
  
// make this compile  
std::unordered_set<boost::uuids::uuid> set;  
```  
  
A fast solution can be the use of existing boost::hash_value:  
  
```  
namespace std  
{  
    template<>  
    struct hash<boost::uuids::uuid>  
    {  
        std::size_t operator ()(const boost::uuids::uuid &value) const noexcept  
        {  
            return boost::hash_value(value);  
        }  
     };  
}  
```
