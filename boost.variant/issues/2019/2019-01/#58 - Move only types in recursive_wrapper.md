# #58 - Move only types in recursive_wrapper [Closed]

> Username: Kojoley  
> Created at: 2019-01-31 16:49:05 UTC  
> Updated at: 2019-04-12 07:52:36 UTC  
> Closed at: 2019-04-12 07:52:36 UTC  
> Url: https://github.com/boostorg/variant/issues/58  

Consider this simplified example:  
```cpp  
#include <boost/variant.hpp>  
#include <memory>  
  
struct A {};  
using ABC = boost::variant<  
	boost::recursive_wrapper<std::unique_ptr<A>>  
>;  
  
int main()  
{  
	ABC a, b = std::make_unique<A>();  
	a = std::move(b);  
}  
```  
*Using unique_ptr in the example as it a known non-copyable but movable type*  
  
This does not compile because variant tries to make a copy of unique_ptr.  
Assuming my knowledge (and variant knows this too) that `recursive_wrapper` contains a pointer to heap allocated object I was expecting that variant assignment will swap pointers (the assignment operator does swap pointers) or in the worst case it would move the object.  
  
*It would be even better if variant always perform on move pointer stealing from other variant with active recursive_wrapper, it is the main thing why spirit::x3::variant exists https://github.com/boostorg/spirit/issues/445#issuecomment-459355870*

---

## Comment 1

> Username: Kojoley  
> Created at: 2019-02-01 14:47:46 UTC  
> Url: https://github.com/boostorg/variant/issues/58#issuecomment-459746331  

Even if #59 is not acceptable there is a way to fix this that way - variant move constructor default constructs a `recursive_wrapper` (in case of exception here simply no variant will be constructed and rhs is untouched) and then move assigns an rhs `recursive_wrapper` (we speaking about the case when move does not throw). From this change all nothrow move constructible `recursive_wrapper`ed types will benefit, because instead of making two copies it will default construct + move which usually should be a way more cheaper.
