# #206 - Data loss during race read from 3 channels [Closed]

> Username: romanfomin  
> Created at: 2024-10-20 18:01:17 UTC  
> Updated at: 2024-10-29 15:44:45 UTC  
> Closed at: 2024-10-29 15:44:45 UTC  
> Url: https://github.com/boostorg/cobalt/issues/206  

This simple example fills 2 channels with values and then reads from these channels using `cobalt::race` and checks data is not lost. This example works, we got all values from both channels printed and no asserts triggered.  
```cpp  
cobalt::main co_main(int argc, char * argv[])  
{  
    cobalt::channel<int> c1 {10};  
    cobalt::channel<int> c2 {10};  
    for (int i = 0; i < 10; i++)  
    {  
        co_await c1.write(i);  
        co_await c2.write(1000 + i);  
    }  
    int i1 = 0;  
    int i2 = 1000;  
    while (true)  
    {  
        auto res = co_await cobalt::race(c1.read(), c2.read());  
        std::cout << res << "\n";  
        switch (res.index())  
        {  
            case 0:  
                assert(boost::variant2::get<0>(res) == i1++);  
                break;  
            case 1:  
                assert(boost::variant2::get<1>(res) == i2++);  
                break;  
        }  
    }  
  
    co_return 0;  
}  
```  
  
But if we add third channel to race function call then some data from channels is lost.  
```cpp  
cobalt::main co_main(int argc, char * argv[])  
{  
    cobalt::channel<int> c1 {10};  
    cobalt::channel<int> c2 {10};  
    cobalt::channel<int> c3 {10};  
    for (int i = 0; i < 10; i++)  
    {  
        co_await c1.write(i);  
        co_await c2.write(1000 + i);  
    }  
    int i1 = 0;  
    int i2 = 1000;  
    while (true)  
    {  
        auto res = co_await cobalt::race(c1.read(), c2.read(), c3.read());  
        std::cout << res << "\n";  
        switch (res.index())  
        {  
            case 0:  
                assert(boost::variant2::get<0>(res) == i1++);  
                break;  
            case 1:  
                assert(boost::variant2::get<1>(res) == i2++);  
                break;  
        }  
    }  
  
    co_return 0;  
}  
```  
In the second example the output is something like:  
```sh  
1000  
0  
1  
1002  
cobalt::main co_main(int, char **): Assertion `boost::variant2::get<1>(res) == i2++' failed.  
```  
Value 1001 is lost.
