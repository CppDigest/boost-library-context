# #291 - buffered_channel calls deleter twice when using iterators [Open]

> Username: YaZasnyal  
> Created at: 2022-02-02 16:32:20 UTC  
> Updated at: 2022-12-28 21:19:44 UTC  
> Url: https://github.com/boostorg/fiber/issues/291  

version: 1.75.0 (probably 1.78.0)  
compiler: gcc 9.4.0  
  
Hi, I have found some unexpected behavior when using ```buffered_channel::iterator```. The destructor is called twice when using them.  
  
This happens first time when ```operator++``` is called and second time in ```increment()```  
https://github.com/boostorg/fiber/blob/8d0fe6055ad2c2cd98a022ad663d2bbd42e056c9/include/boost/fiber/buffered_channel.hpp#L357-L361  
https://github.com/boostorg/fiber/blob/8d0fe6055ad2c2cd98a022ad663d2bbd42e056c9/include/boost/fiber/buffered_channel.hpp#L310-L320  
  
Got this on version 1.75.0 but it seems that it is still present.  
  
Minimal reproducible example:  
```cpp  
#include <memory>  
#include <vector>  
  
#include <boost/fiber/all.hpp>  
  
class Foo  
{  
public:  
  Foo(int i) : i_(i) {std::cout << "Foo()\n";}  
  ~Foo() {std::cout << "~Foo()\n";}  
  void PrintI() {std::cout << "Foo::operator()(): " << i_ << "\n";}  
  
private:  
  int i_ = 0;  
};  
using FooSptr = std::shared_ptr<Foo>;  
  
int main(int, char**)  
{  
  boost::fibers::buffered_channel<FooSptr> chan(16);  
  
  boost::fibers::fiber f([&]() {  
    std::vector<FooSptr> fooVec; // keep objects for some time  
    for(auto& foo : chan)  
    {  
      fooVec.push_back(foo);  
    }  
  
    for (auto& foo : fooVec)  
    {  
      foo->PrintI();  
    }  
    fooVec.clear();  
    std::cout << "Fiber finished\n";  
  });  
  
  chan.push(std::make_shared<Foo>(0));  
  chan.push(std::make_shared<Foo>(1));  
  
  chan.close();  
  std::cout << "chan.close();\n";  
  
  f.join();  
  
  return 0;  
}  
```  
  
In this example I pass some shared_ptr's through the channel and put it in an array to extend it's lifetime. But counter decrements twice and objects gets destroyed.   
  
Expected result:  
```  
Foo()  
Foo()  
chan.close();  
Foo::operator()(): 0  
Foo::operator()(): 1  
~Foo()  
~Foo()  
Fiber finished  
```  
  
Actual result:  
```  
Foo()  
Foo()  
chan.close();  
~Foo()  
~Foo()  
Foo::operator()(): 32  
Foo::operator()(): 1953066354  
Fiber finished  
```

---

## Comment 1

> Username: YaZasnyal  
> Created at: 2022-02-02 16:47:20 UTC  
> Url: https://github.com/boostorg/fiber/issues/291#issuecomment-1028137802  

I made a little investigation and think that https://github.com/boostorg/fiber/commit/37b5f770fa3431e086c029c7fc19190c17213ff4 (#209) should be reverted because https://github.com/boostorg/fiber/commit/e44062381452df4acc4947f213d82a1c65ff4b4a (#258) already covers all situations where deletion is required.

---

## Comment 2

> Username: ttti07  
> Created at: 2022-12-28 06:35:52 UTC  
> Updated at: 2022-12-28 06:40:05 UTC  
> Url: https://github.com/boostorg/fiber/issues/291#issuecomment-1366406668  

I'm suffering the same issue, so double free corruption is occuring. (my current boost version is 1.80)  
Even `boost::fibers::buffered_channel<std::string>` does not work at all, which used to work well in previous versions. (maybe <=1.70)  
I think there must be some test cases for `T = std::string` or `T = std::shared_ptr<X>` in test_buffered_channel_post.cpp file...

---

## Comment 3

> Username: YaZasnyal  
> Created at: 2022-12-28 21:19:44 UTC  
> Url: https://github.com/boostorg/fiber/issues/291#issuecomment-1366921911  

Unfortunately this problem is still present but there is a workaround. You still can pop elements like this:  
```cpp  
FooSptr foo;  
while(chan.pop(foo) == boost::fibers::channel_op_status::success)  
{  
  fooVec.push_back(foo);  
}  
```
