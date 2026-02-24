# #25 - Compile Error [Closed]

> Username: jaanisfehling  
> Created at: 2025-01-20 18:57:35 UTC  
> Updated at: 2025-01-28 09:37:14 UTC  
> Closed at: 2025-01-28 09:37:13 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/25  

Getting this error when including the headers (not even using the functions from the library!)  
I am compiling using gcc 13 and C++23 on alpine linux.  
I am confused since none of the errors are actually happening in my code.  
  
```  
/workspaces/guidance-v3/build/_deps/async_mqtt5-src/include/async_mqtt5/impl/codecs/base_encoders.hpp: In instantiation of 'auto async_mqtt5::encoders::basic::flag_def<bits, repr>::operator()(T&&, projection) const [with T = async_mqtt5::encoders::basic::flag_def<4, unsigned char>; projection = boost::identity; typename std::enable_if<(! is_optional<T>), bool>::type <anonymous> = true; long unsigned int bits = 4; repr = unsigned char]':  
/usr/include/boost/asio/deferred.hpp:697:41:   required by substitution of 'template<class Head, class Tail> boost::asio::constraint_t<boost::asio::is_deferred<T>::value, decltype (static_cast<Head&&>(head)(static_cast<Tail&&>(tail)))> boost::asio::operator|(Head, Tail&&) [with Head = async_mqtt5::encoders::basic::flag_def<4, unsigned char>; Tail = async_mqtt5::encoders::basic::flag_def<4, unsigned char>]'  
/workspaces/guidance-v3/build/_deps/async_mqtt5-src/include/async_mqtt5/impl/codecs/message_encoders.hpp:41:19:   required from here  
/workspaces/guidance-v3/build/_deps/async_mqtt5-src/include/async_mqtt5/impl/codecs/base_encoders.hpp:95:28: error: invalid 'static_cast' from type 'async_mqtt5::encoders::basic::flag_def<4, unsigned char>' to type 'unsigned char'  
   95 |                 auto val = static_cast<repr>(std::invoke(proj, value));  
      |                            ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/workspaces/guidance-v3/build/_deps/async_mqtt5-src/include/async_mqtt5/impl/codecs/base_encoders.hpp:96:51: error: no matching function for call to 'async_mqtt5::encoders::basic::flag_def<4, unsigned char>::flag_def(<brace-enclosed initializer list>)'  
   96 |                 return flag_def<bits, repr> { val };  
      |                                                   ^  
/workspaces/guidance-v3/build/_deps/async_mqtt5-src/include/async_mqtt5/impl/codecs/base_encoders.hpp:70:9: note: candidate: 'constexpr async_mqtt5::encoders::basic::flag_def<bits, repr>::flag_def() [with long unsigned int bits = 4; repr = unsigned char]'  
   70 |         flag_def() = default;  
      |         ^~~~~~~~  
/workspaces/guidance-v3/build/_deps/async_mqtt5-src/include/async_mqtt5/impl/codecs/base_encoders.hpp:70:9: note:   candidate expects 0 arguments, 1 provided  
/workspaces/guidance-v3/build/_deps/async_mqtt5-src/include/async_mqtt5/impl/codecs/base_encoders.hpp:69:9: note: candidate: 'async_mqtt5::encoders::basic::flag_def<bits, repr>::flag_def(repr) [with long unsigned int bits = 4; repr = unsigned char]'  
   69 |         flag_def(repr val) : _val(val) {}  
      |         ^~~~~~~~  
/workspaces/guidance-v3/build/_deps/async_mqtt5-src/include/async_mqtt5/impl/codecs/base_encoders.hpp:69:9: note:   conversion of argument 1 would be ill-formed:  
/workspaces/guidance-v3/build/_deps/async_mqtt5-src/include/async_mqtt5/impl/codecs/base_encoders.hpp:50:7: note: candidate: 'constexpr async_mqtt5::encoders::basic::flag_def<4, unsigned char>::flag_def(const async_mqtt5::encoders::basic::flag_def<4, unsigned char>&)'  
   50 | class flag_def : public encoder {  
      |       ^~~~~~~~  
/workspaces/guidance-v3/build/_deps/async_mqtt5-src/include/async_mqtt5/impl/codecs/base_encoders.hpp:50:7: note:   conversion of argument 1 would be ill-formed:  
/workspaces/guidance-v3/build/_deps/async_mqtt5-src/include/async_mqtt5/impl/codecs/base_encoders.hpp:50:7: note: candidate: 'constexpr async_mqtt5::encoders::basic::flag_def<4, unsigned char>::flag_def(async_mqtt5::encoders::basic::flag_def<4, unsigned char>&&)'  
/workspaces/guidance-v3/build/_deps/async_mqtt5-src/include/async_mqtt5/impl/codecs/base_encoders.hpp:50:7: note:   conversion of argument 1 would be ill-formed:  
/workspaces/guidance-v3/build/_deps/async_mqtt5-src/include/async_mqtt5/impl/codecs/base_encoders.hpp: In instantiation of 'auto async_mqtt5::encoders::basic::flag_def<bits, repr>::operator()(T&&, projection) const [with T = async_mqtt5::encoders::basic::flag_def<1, unsigned char>; projection = boost::identity; typename std::enable_if<(! is_optional<T>), bool>::type <anonymous> = true; long unsigned int bits = 1; repr = unsigned char]':  
/usr/include/boost/asio/deferred.hpp:697:41:   required by substitution of 'template<class Head, class Tail> boost::asio::constraint_t<boost::asio::is_deferred<T>::value, decltype (static_cast<Head&&>(head)(static_cast<Tail&&>(tail)))> boost::asio::operator|(Head, Tail&&) [with Head = async_mqtt5::encoders::basic::flag_def<1, unsigned char>; Tail = async_mqtt5::encoders::basic::flag_def<1, unsigned char>]'  
/workspaces/guidance-v3/build/_deps/async_mqtt5-src/include/async_mqtt5/impl/codecs/message_encoders.hpp:45:26:   required from here  
```  
  
I included the following headers:  
```  
#include <async_mqtt5/mqtt_client.hpp>  
#include <async_mqtt5/types.hpp>  
```

---

## Comment 1

> Username: biljazovic  
> Created at: 2025-01-21 11:14:15 UTC  
> Updated at: 2025-01-21 11:21:17 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/25#issuecomment-2604433023  

Hi, thanks for reporting the issue! I wasn't able to reproduce this with GCC 13.3.1, C++23 and Boost versions 1.82, 1.84, 1.86 and 1.87.  
  
Could you please share which version of Boost you're using?

---

## Comment 2

> Username: jaanisfehling  
> Created at: 2025-01-23 13:40:30 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/25#issuecomment-2609838629  

I am using Boost 1.84.  
I am unfortunatly struggling to build a minimal reproducibale example. Other than boost and async-mqtt5, i have dependencies to GeographicLib and SQLite3.  
I tried compiling the relevant MQTT code in my application alone and the error did not appear.  
  
I am using lots of boost libraries (json, logging, asio, beast...) but none have any issues.

---

## Comment 3

> Username: biljazovic  
> Created at: 2025-01-28 09:08:51 UTC  
> Updated at: 2025-01-28 09:09:12 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/25#issuecomment-2618371467  

I can reproduce the error with following code:  
  
```cpp  
#include <boost/asio/deferred.hpp>  
  
using namespace boost::asio;  
  
#include <async_mqtt5/mqtt_client.hpp>  
#include <async_mqtt5/types.hpp>  
  
int main() {  
  
}  
```  
  
with `using namespace boost::asio`, `operator|` from `<boost/asio/deferred.hpp>` ends up being a candidate with parameters of our type `flag_def `from `async_mqtt5::encoders::basic` namespace.  
  
Could this be happening in your case?

---

## Comment 4

> Username: jaanisfehling  
> Created at: 2025-01-28 09:37:13 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/25#issuecomment-2618433123  

Yes, probably that is the case, thank you. Although meanwhile I switched to Paho :((
