# #82 - Is conversion from result<T> to result<U> a valid use-case? [Closed]

> Username: ytimenkov  
> Created at: 2022-04-19 13:15:20 UTC  
> Updated at: 2022-06-30 01:14:33 UTC  
> Closed at: 2022-06-30 01:14:33 UTC  
> Url: https://github.com/boostorg/system/issues/82  

Working with generic code it may be beneficial if `result<T>` can be implicitly converted to `result<U>` if such conversion is allowed for underlying `T` and `U`.  
  
For example:  
  
```c++  
void f(result<std::size_t> ec)  
{  
}  
  
template<typename T>  
void g(T&& arg)  
{  
    f(result<std::decay_t<T>>{arg});  
}  
  
int main()  
{  
    g(0);  
  
    return 0;  
}  
```  
  
results in error  
```  
error: could not convert 'boost::system::result<int, boost::system::error_code>((* & arg))' from 'result<int,[...]>' to 'result<long unsigned int,[...]>'  
```  
  
This mostly needed for generic code, where it is impossible (or at least very hard) which type the final function really accepts and argument (or result) may be passed through multiple forwarding functions.

---

## Comment 1

> Username: pdimov  
> Created at: 2022-06-18 20:08:53 UTC  
> Url: https://github.com/boostorg/system/issues/82#issuecomment-1159552597  

The converting constructor is implemented on the develop branch now.

---

## Comment 2

> Username: pdimov  
> Created at: 2022-06-30 01:14:33 UTC  
> Url: https://github.com/boostorg/system/issues/82#issuecomment-1170645213  

Has been merged to master, should be in 1.80.
