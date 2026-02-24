# #101 - NULL deference exception in boost::asio::ip::tcp::resolver::results_type [Closed]

> Username: jackpoz  
> Created at: 2018-04-27 19:53:02 UTC  
> Updated at: 2020-12-30 00:50:34 UTC  
> Closed at: 2020-12-30 00:50:33 UTC  
> Url: https://github.com/boostorg/asio/issues/101  

boost::asio::ip::tcp::resolver::results_type type triggers a NULL dereference exception when calling empty() on a default-initialized instance.  
  
How to reproduce:  
- instantiate a new boost::asio::ip::tcp::resolver::results_type class instance with the default parameterless constructor  
- call the empty() method, it should return true  
- notice the access violation crash triggered  
  
Code example:  
```cpp  
#include <boost/asio/ip/tcp.hpp>  
  
int main(int /*argc*/, char** /*argv*/)  
{  
  boost::asio::ip::tcp::resolver::results_type test = boost::asio::ip::tcp::resolver::results_type();  
  if (test.empty())  
    printf("ok");  
}  
```  
  
Boost version: 1.66 Windows x64 vc141  
  
Issue analysis: [empty()](https://github.com/boostorg/asio/blob/b61b55515706a8071b16708c095a641b92357f2c/include/boost/asio/ip/basic_resolver_results.hpp#L246) is implemented as   
```cpp  
bool empty() const BOOST_ASIO_NOEXCEPT  
{  
  return this->values_->empty();  
}  
```  
but this->values_ returns NULL when the class is empty. A possible fix could be to NULL-check this->values_ and then call ->empty() (if still needed at all)  
```cpp  
bool empty() const BOOST_ASIO_NOEXCEPT  
{  
  return !this->values_ || this->values_->empty();  
}  
```  
  
Current workaround: I would suggest not to use .empty() at all currently but to rely on .begin() == .end() .  
  
Note that even if the code example above looks quite meaningless, .empty() crashes causes issue for example when checking if an address can be resolved using [ip::basic_resolver::resolve](https://www.boost.org/doc/libs/1_66_0/doc/html/boost_asio/reference/ip__basic_resolver/resolve/overload8.html) function. The documentation states "An empty range is returned if an error occurs" so one expects to be able to check if the range is empty.

---

## Comment 1

> Username: jackpoz  
> Created at: 2018-05-24 20:33:25 UTC  
> Url: https://github.com/boostorg/asio/issues/101#issuecomment-391850225  

is there something missing in my issue description that I could add to speed it up ?

---

## Comment 2

> Username: jackpoz  
> Created at: 2018-10-31 10:07:26 UTC  
> Url: https://github.com/boostorg/asio/issues/101#issuecomment-434630280  

@chriskohlhoff do you need any other information or do you want me to pull request the fix or do you have any comment at all about this issue (or the status/future of this project, for the matter) ?

---

## Comment 3

> Username: ghost  
> Created at: 2020-12-30 00:50:32 UTC  
> Url: https://github.com/boostorg/asio/issues/101#issuecomment-752289361  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#608](https://github.com/chriskohlhoff/asio/issues/608).
