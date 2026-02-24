# #143 - Cannot advance an iterator to an empty deque by zero, or take an empty slice. [Closed]

> Username: cs96and  
> Created at: 2020-02-14 12:38:18 UTC  
> Updated at: 2020-05-25 00:00:36 UTC  
> Closed at: 2020-05-25 00:00:36 UTC  
> Url: https://github.com/boostorg/container/issues/143  

With Boost 1.72.0, the following code causes an assert under debug, or a divide by zero for release builds...  
```c++  
boost::container::deque<int> myDeque;  
auto iter = myDeque.begin();  
std::advance(iter, 0);  
```  
  
This happens when trying to take a zero sized slice of an empty deque...  
```c++  
boost::container::deque<int> myDeque;  
nItems = 0;  
for(const auto &item : myDeque | boost::adaptors::sliced(0, nItems))  
{  
    // do something  
}  
```  
  
This looks similar to #83 which was a similar issue but for vectors instead of deques.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2020-05-25 00:00:36 UTC  
> Url: https://github.com/boostorg/container/issues/143#issuecomment-633319500  

Thanks for the report. This was fixed in https://github.com/boostorg/container/pull/137 and will be released for Boost 1.74.
