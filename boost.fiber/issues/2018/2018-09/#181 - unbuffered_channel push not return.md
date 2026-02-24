# #181 - unbuffered_channel push not return [Closed]

> Username: jxfwinter  
> Created at: 2018-09-21 07:35:07 UTC  
> Updated at: 2018-10-06 06:38:03 UTC  
> Closed at: 2018-10-06 06:38:03 UTC  
> Url: https://github.com/boostorg/fiber/issues/181  

example code:  
  
```  
#include <chrono>  
#include <iostream>  
#include <string>  
#include <boost/fiber/all.hpp>  
  
void test_channel()  
{  
    using namespace std;  
    using namespace boost;  
    fibers::unbuffered_channel<string> ch;  
  
    fibers::fiber f1([&ch]() {  
        cout << "before push\n";  
        boost::fibers::channel_op_status s = ch.push(string("test"));  
        cout << "after push, status:" << static_cast<int>(s) << "\n";  
    });  
  
  
    fibers::fiber f2([&ch]() {  
        boost::this_fiber::sleep_for(chrono::seconds(1));  
        ch.close();  
    });  
  
    f2.join();  
    cout << "f2 stop\n";  
    f1.join();  
    cout << "f1 stop\n";  
}  
  
  
int main( int argc, char *argv[])  
{  
    boost::fibers::use_scheduling_algorithm<boost::fibers::algo::round_robin>();  
    test_channel();  
  
    return EXIT_SUCCESS;  
}  
```  
output:  
```  
[root@localhost bin]# ./test_case  
before push  
f2 stop  
```  
  
the f1 fiber will never end

---

## Comment 1

> Username: olk  
> Created at: 2018-09-21 19:21:25 UTC  
> Url: https://github.com/boostorg/fiber/issues/181#issuecomment-423644890  

producer is suspended till a consumer has dequeued the value  
the producer is not resumed if close() is called (-> fix required)

---

## Comment 2

> Username: olk  
> Created at: 2018-10-06 06:38:03 UTC  
> Url: https://github.com/boostorg/fiber/issues/181#issuecomment-427550820  

ty
