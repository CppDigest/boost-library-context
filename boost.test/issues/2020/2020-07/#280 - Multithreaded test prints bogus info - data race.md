# #280 - Multithreaded test prints bogus info - data race? [Closed]

> Username: AlexGuteniev  
> Created at: 2020-07-22 13:04:50 UTC  
> Updated at: 2022-03-05 22:08:42 UTC  
> Closed at: 2022-03-05 22:08:34 UTC  
> Url: https://github.com/boostorg/test/issues/280  

Windows 10, Visual Studio 2015, default output verbosity.  
  
The following test expected to print nothing:  
```C++  
   BOOST_AUTO_TEST_CASE(ThreadSafety)  
    {  
        int a = 1, b = 1;  
        std::thread t1([a, b] { for(int i = 1; i < 100'000; i++) { BOOST_CHECK_EQUAL(a, b); } });  
        std::thread t2([a, b] { for(int i = 1; i < 100'000; i++) { BOOST_CHECK_EQUAL(a, b); } });  
        t1.join();  
        t2.join();  
    }  
```  
  
In release mode (usual VS 2015 release configuration) it prints:  
```  
TestQueue2.cpp(133): info:  has passed  
  
TestQueue2.cpp(133): info:  has passed  
  
TestQueue2.cpp(134): info: a  
  
TestQueue2.cpp(134): info:  has passed  
  
TestQueue2.cpp(134): info: a  
  
TestQueue2.cpp(133): info: b  
  
 estQueue2.cpp(134): info: check  
  
TestQueue2.cpp(134): info: check  
  
TestQueue2.cpp(134): info: check  
  
TestQueue2.cpp(134): info: check  
  
TestQueue2.cpp(134): info:  has passed  
  
TestQueue2.cpp(133): info: check  
  
TestQueue2.cpp(133): info:  ==  
```

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2022-03-05 22:08:34 UTC  
> Url: https://github.com/boostorg/test/issues/280#issuecomment-1059838969  

Currently Boost.Test is not thread safe. This duplicates #304 , closing.
