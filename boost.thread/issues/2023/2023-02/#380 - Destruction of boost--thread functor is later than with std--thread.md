# #380 - Destruction of boost::thread functor is later than with std::thread [Open]

> Username: mika-fischer  
> Created at: 2023-02-13 22:30:54 UTC  
> Updated at: 2023-02-13 22:30:54 UTC  
> Url: https://github.com/boostorg/thread/issues/380  

It seems that `boost::thread` destroys its functor only when join is called. `std::thread` destroys its functor immediately after the functor returns. Is there a reason for this discrepancy?  
  
Using RAII patterns this can lead to surprising deadlocks...  
  
Example code:  
```cpp  
#include <boost/thread/thread.hpp>  
  
#include <cstdio>  
#include <thread>  
#include <utility>  
  
struct foo {  
    const char* name_ = nullptr;  
    foo(const char* name) : name_(name) { printf("%s foo()\n", name); }  
    foo(foo&& o) noexcept : name_(std::exchange(o.name_, nullptr)) {}  
    foo(foo& o) = delete;  
    ~foo() {  
        if (name_) {  
            printf("%s ~foo()\n", name_);  
        }  
    }  
};  
  
template <typename Thread>  
void test(const char* name) {  
    printf("%s starting\n", name);  
    auto thread = Thread([&, f = foo(name)] { printf("%s done\n", name); });  
    std::this_thread::sleep_for(std::chrono::milliseconds(100));  
    printf("%s joining\n", name);  
    thread.join();  
    printf("%s joined\n\n", name);  
}  
  
int main() {  
    test<std::thread>("std::thread");  
    test<boost::thread>("boost::thread");  
}  
```  
  
Output:  
```  
std::thread starting  
std::thread foo()  
std::thread done  
std::thread ~foo()  
std::thread joining  
std::thread joined  
  
boost::thread starting  
boost::thread foo()  
boost::thread done  
boost::thread joining  
boost::thread ~foo()  
boost::thread joined  
```  
  
[Wandbox](https://wandbox.org/permlink/kmRO1o8Yq9DV9SCd)
