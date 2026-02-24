# #207 - BOOST_LOG_TRIVIAL cannot find overloaded `operator<<()` when it's declared in global scope [Closed]

> Username: vehlwn  
> Created at: 2023-04-03 15:19:25 UTC  
> Updated at: 2023-04-04 04:02:12 UTC  
> Closed at: 2023-04-03 15:58:55 UTC  
> Url: https://github.com/boostorg/log/issues/207  

BOOST_LOG_TRIVIAL and BOOST_LOG behavior seems not to be equivalent to `std::cout` and other standard iostreams. Here is an example:  
  
main.cpp:  
  
```c++  
#include <iostream>  
  
#include <boost/log/sources/logger.hpp>  
#include <boost/log/trivial.hpp>  
  
namespace test {  
struct A {};  
} // namespace test  
  
std::ostream &operator<<(std::ostream &os, const test::A &) {  
  os << "A";  
  return os;  
}  
  
int main() {  
  test::A a;  
  std::clog << a << std::endl;  
  
  namespace src = boost::log::sources;  
  src::logger lg;  
  BOOST_LOG_TRIVIAL(info) << a;  
  BOOST_LOG(lg) << a;  
}  
```  
  
meson.build:  
  
```meson  
project('boost-log-mre', ['cpp'], default_options: ['cpp_std=c++17'])  
boost_dep = dependency('boost', modules: ['log'], include_type: 'system')  
executable( 'a', ['main.cpp'], dependencies: [boost_dep])  
```  
  
build:  
  
```bash  
$ mkdir build && cd build  
$ meson setup --buildtype debug  
$ meson compile  
```  
  
Arch Linux, boost 1.81.0-3, gcc version 12.2.1 20230201, clang version 15.0.7  
  
It fails to compile on GCC and Clang at BOOST_LOG_TRIVIAL and BOOST_LOG lines due to missing overload:  
  
>/usr/include/boost/log/utility/formatting_ostream.hpp:929:19: error: no match for ‘operator<<’ (operand types are ‘boost::log::v2_mt_posix::basic_formatting_ostream<char>::ostream_type’ {aka ‘std::basic_ostream<ch  
ar>’} and ‘test::A’)  
  929 |     strm.stream() << value;  
  
But when I move definition of `operator<<()` into namespace `test`, it magically fixes itself. `std::clog` line works well in both cases. Is this intentional behavior or a bug in boost::log? I've stumbled upon this problem when tried to output `Poco::Net::SocketAddress` into BOOST_LOG_TRIVIAL because it declares it's overloaded `operator<<()` in a global scope and not in a scope of SocketAddress.  
  
https://github.com/pocoproject/poco/blob/1211613642269b7d53bea58b02de7fcd25ece3b9/Net/include/Poco/Net/SocketAddress.h#L304

---

## Comment 1

> Username: Lastique  
> Created at: 2023-04-03 15:58:55 UTC  
> Url: https://github.com/boostorg/log/issues/207#issuecomment-1494584926  

This behavior is intended and by design. The `operator<<` for the class `A` must be findable using [ADL](https://en.cppreference.com/w/cpp/language/adl), meaning it should probably be defined in namespace `test`.  
  
The fact that it works with `std::clog` is because the operator is visible from the context of the streaming expression. With Boost.Log, the `operator<<` you're calling from the streaming expression is not the one you declared in the global namespace but one of the forwarding operators provided by Boost.Log in its namespace. These forwarding operators shadow the `operator<<` in the global namespace, and ADL does not find it either as it is not in any of the associated namespaces.  
  
The code in your example is rather fragile because it can easily break due to the operator being shadowed by other operators defined for other types. [Here](https://gcc.godbolt.org/z/19znnWoK9)'s an example.

---

## Comment 2

> Username: vehlwn  
> Created at: 2023-04-04 04:02:11 UTC  
> Url: https://github.com/boostorg/log/issues/207#issuecomment-1495314352  

Thanks for the explanation! Then I'll create issue in the Poco repo.
