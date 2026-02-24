# #185 - join.cpp in example directory seems to be confusing [Closed]

> Username: JohnCoconut  
> Created at: 2018-11-01 08:39:56 UTC  
> Updated at: 2018-11-01 09:07:31 UTC  
> Closed at: 2018-11-01 09:07:31 UTC  
> Url: https://github.com/boostorg/fiber/issues/185  

```cpp  
void fn2( boost::fibers::fiber & f)  
{  
    boost::fibers::fiber::id id = boost::this_fiber::get_id();  
    for ( int i = 0; i < 5; ++i)  
    {  
        ++value2;  
        std::cout << "fiber " << id << " fn2: increment value2: " << value2 << std::endl;  
        if ( i == 1)  
        {  
            boost::fibers::fiber::id id = f.get_id();  
            std::cout << "fiber " << id << " fn2: joins fiber " << id << std::endl;  
            f.join();  
            std::cout << "fiber " << id << " fn2: joined fiber " << id << std::endl;  
        }  
        boost::this_fiber::yield();  
    }  
    std::cout << "fiber " << id << " fn2: returns" << std::endl;  
}  
```  
  
I am learning boost fiber library at the moment. I undertand the semantics of this `join.cpp` example. But the following piece of code is confusing when I first looked at it,  
```cpp  
std::cout << "fiber " << id << " fn2: joins fiber " << id << std::endl;  
```  
  
Isn't it trying to print something like this,  
  
> fiber **fiber2_id** fn2: joins fiber **fiber1_id**  
  
instead of  
  
> fiber **fiber1_id** fn2: joins fiber **fiber1_id**  
  
And I feel it is more obvious if we change the order a bit,  
  
> fiber **fiber1_id** fn2: fiber **fiber1_id** joins  
  
  
The output before the change,  
```bash  
fiber 0x7ffba4eeff00 fn1: increment value1: 1  
fiber 0x7ffba487bf00 fn2: increment value2: 1  
fiber 0x7ffba4eeff00 fn1: increment value1: 2  
fiber 0x7ffba487bf00 fn2: increment value2: 2  
fiber 0x7ffba4eeff00 fn2: joins fiber 0x7ffba4eeff00  
fiber 0x7ffba4eeff00 fn1: increment value1: 3  
fiber 0x7ffba4eeff00 fn1: increment value1: 4  
fiber 0x7ffba4eeff00 fn1: increment value1: 5  
fiber 0x7ffba4eeff00 fn1: returns  
fiber 0x7ffba4eeff00 fn2: joined fiber 0x7ffba4eeff00  
fiber 0x7ffba487bf00 fn2: increment value2: 3  
fiber 0x7ffba487bf00 fn2: increment value2: 4  
fiber 0x7ffba487bf00 fn2: increment value2: 5  
fiber 0x7ffba487bf00 fn2: returns  
done.  
```  
  
The output after the change,  
```bash  
fiber 0x7f9891cb7f00 fn1: increment value1: 1  
fiber 0x7f9891643f00 fn2: increment value2: 1  
fiber 0x7f9891cb7f00 fn1: increment value1: 2  
fiber 0x7f9891643f00 fn2: increment value2: 2  
fiber 0x7f9891643f00 fn2: fiber 0x7f9891cb7f00 joins  
fiber 0x7f9891cb7f00 fn1: increment value1: 3  
fiber 0x7f9891cb7f00 fn1: increment value1: 4  
fiber 0x7f9891cb7f00 fn1: increment value1: 5  
fiber 0x7f9891643f00 fn2: fiber 0x7f9891cb7f00 joined  
fiber 0x7f9891643f00 fn2: increment value2: 3  
fiber 0x7f9891643f00 fn2: increment value2: 4  
fiber 0x7f9891643f00 fn2: increment value2: 5  
fiber 0x7f9891643f00 fn2: returns  
done.   
```

---

## Comment 1

> Username: olk  
> Created at: 2018-11-01 09:07:31 UTC  
> Url: https://github.com/boostorg/fiber/issues/185#issuecomment-434978169  

copy&paste mistake ... variable `id` was overwritten; `this_id` == id of the current fiber, `id` == fiber that will be joined  
```  
void fn2( boost::fibers::fiber & f)  
{  
    boost::fibers::fiber::id this_id = boost::this_fiber::get_id();  
    for ( int i = 0; i < 5; ++i)  
    {  
        ++value2;  
        std::cout << "fiber " << this_id << " fn2: increment value2: " << value2 << std::endl;  
        if ( i == 1)  
        {  
            boost::fibers::fiber::id id = f.get_id();  
            std::cout << "fiber " << this_id << " fn2: joins fiber " << id << std::endl;  
            f.join();  
            std::cout << "fiber " << this_id << " fn2: joined fiber " << id << std::endl;  
        }  
        boost::this_fiber::yield();  
    }  
    std::cout << "fiber " << this_id << " fn2: returns" << std::endl;  
}  
```  
Out put will be:  
```  
fiber 0x7fd33b949f00 fn1: increment value1: 1  
fiber 0x7fd33b3d2f00 fn2: increment value2: 1  
fiber 0x7fd33b949f00 fn1: increment value1: 2  
fiber 0x7fd33b3d2f00 fn2: increment value2: 2  
fiber 0x7fd33b3d2f00 fn2: joins fiber 0x7fd33b949f00  
fiber 0x7fd33b949f00 fn1: increment value1: 3  
fiber 0x7fd33b949f00 fn1: increment value1: 4  
fiber 0x7fd33b949f00 fn1: increment value1: 5  
fiber 0x7fd33b949f00 fn1: returns  
fiber 0x7fd33b3d2f00 fn2: joined fiber 0x7fd33b949f00  
fiber 0x7fd33b3d2f00 fn2: increment value2: 3  
fiber 0x7fd33b3d2f00 fn2: increment value2: 4  
fiber 0x7fd33b3d2f00 fn2: increment value2: 5  
fiber 0x7fd33b3d2f00 fn2: returns  
done.  
```
