# #225 - make_map<int>(make_vector()) cannot be compiled [Open]

> Username: skhrshin  
> Created at: 2020-09-05 16:44:18 UTC  
> Updated at: 2020-09-06 12:56:25 UTC  
> Url: https://github.com/boostorg/fusion/issues/225  

I'm about to make a `boost::fusion::map<boost::fusion::pair<int, boost::fusion::vector<>>>` object, but the following code won't be compiled.  
```  
#include <boost/fusion/include/make_map.hpp>  
#include <boost/fusion/include/make_vector.hpp>  
  
int main() {  
    boost::fusion::make_map<int>(boost::fusion::make_vector());  
}  
```  
You can try it here: https://wandbox.org/permlink/9t5X0yHFBW5eZ2xC  
  
Since `boost::fusion::map` looked to be doing something special if passed data is sequence, I took a look at the document but could find nothing about it. What's wrong?

---

## Comment 1

> Username: djowel  
> Created at: 2020-09-05 22:49:02 UTC  
> Url: https://github.com/boostorg/fusion/issues/225#issuecomment-687672684  

It looks like a bug with `make_map`. In the meantime, a workaround not using `make_map`, but directly using `map`. Something like:  
  
```c++  
#include <boost/fusion/include/make_map.hpp>  
#include <boost/fusion/include/make_vector.hpp>  
#include <string>  
  
using namespace boost::fusion;  
  
typedef map<  
    pair<int, vector<>> >  
map_type;  
  
map_type m(make_pair<int>(vector<>()));  
```  
  
A PR is very welcome if you can investigate some more on fixing this.

---

## Comment 2

> Username: skhrshin  
> Created at: 2020-09-06 09:13:55 UTC  
> Updated at: 2020-09-06 11:01:20 UTC  
> Url: https://github.com/boostorg/fusion/issues/225#issuecomment-687734780  

Thanks for telling me a workaround! I could make my own make_map function using `map`, `pair` and it is working fine with `make_vector`.  
  
The original `make_map` doesn't pass `pair` object to the ctor of `map`. That is the cause of this issue where any object whose `traits::is_sequence<T>::value` will be `true` cannot be passed. Modifying `arg` to `fusion::pair<Key, typename detail::as_fusion_element<T>::type>(arg)` will fix it, but now I wonder why `arg` is lvalue reference rather than forwarding reference. Maybe it's related with some compatibility issues? I haven't understood much enough to make a PR for it yet.

---

## Comment 3

> Username: djowel  
> Created at: 2020-09-06 12:56:25 UTC  
> Url: https://github.com/boostorg/fusion/issues/225#issuecomment-687782740  

> I wonder why `arg` is lvalue reference rather than forwarding reference  
  
Well, actually fusion predates c++11. There are still some things need to be reworked, but care must be taken because fusion still has to support older compilers, although I am starting to think that maybe it's time to move on.
