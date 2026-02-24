# #120 - basic_resolver_results::empty() on empty object leads to access violation [Closed]

> Username: Zombie-3000  
> Created at: 2018-05-30 16:19:07 UTC  
> Updated at: 2020-12-30 00:51:49 UTC  
> Closed at: 2020-12-30 00:51:48 UTC  
> Url: https://github.com/boostorg/asio/issues/120  

Hi,  
  
this small program:  
```Cpp  
#include <cassert>  
#include <boost/asio.hpp>  
  
void main() {  
	boost::asio::ip::tcp::resolver::results_type results;  
	assert(results.empty());  
}  
```  
leads to a access violation.  
  
**Possible solution:**  
  
change line 248 in basic_resolver_results.hpp from  
```Cpp  
return this->values_->empty();  
```  
to  
```Cpp  
return this->values_ ? this->values_->empty() : true;  
```  
And maybe similar for `basic_resolver_results::size()` and `basic_resolver_results::max_size()`, etc...  
  
thank you

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 00:51:47 UTC  
> Url: https://github.com/boostorg/asio/issues/120#issuecomment-752289608  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#623](https://github.com/chriskohlhoff/asio/issues/623).
