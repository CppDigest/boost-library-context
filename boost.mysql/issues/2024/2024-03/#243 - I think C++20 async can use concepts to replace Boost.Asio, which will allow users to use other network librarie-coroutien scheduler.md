# #243 - I think C++20 async can use concepts to replace Boost.Asio, which will allow users to use other network librarie/coroutien scheduler. [Closed]

> Username: sxstd001  
> Created at: 2024-03-20 15:16:29 UTC  
> Updated at: 2024-03-20 15:45:16 UTC  
> Closed at: 2024-03-20 15:45:10 UTC  
> Url: https://github.com/boostorg/mysql/issues/243  

like this,  use some concepts instead Boost.Asio  
```cpp  
	template<class T>  
	concept TcpConn= requires(T t, char* buf, int num, int offset){  
		co_await t.read(buf, num);  
		co_await  t.read(buf, num, offset);  
		co_await  t.write(buf, num);  
	         co_await  t.close();  
	        t.nowaitClose();  
	       co_await   t.connect....  
              ..........  
	};  
```

---

## Comment 1

> Username: anarthal  
> Created at: 2024-03-20 15:28:47 UTC  
> Url: https://github.com/boostorg/mysql/issues/243#issuecomment-2009848189  

I'm afraid I'm not following - is this a feature request to implement a new, coroutine-only, network-library agnostic interface for this library? Could you please elaborate on your requirements (what coroutine library/scheduler would you want to use, which kind of application would you be using the library for...)?  
  
The issue title says "replace", but the minimum C++ required for this library is C++11 - so the best we can do is to "add" a new interface.  
  
Regards,  
Ruben.
