# #402 - Warning when using std::unique_ptr<T[N]> [Closed]

> Username: xiangfan-ms  
> Created at: 2018-05-29 21:19:23 UTC  
> Updated at: 2018-06-04 18:35:03 UTC  
> Closed at: 2018-06-04 18:35:03 UTC  
> Url: https://github.com/boostorg/hana/issues/402  

MSVC will emit a warning when compiling std::unique_ptr<T[N]> because default_delete<T[N]> has undefined behavior. See https://github.com/boostorg/hana/blob/master/test/tuple/smart_ptr.cpp for details.  
  
Other compilers don't complain likely because warnings are suppress for system headers.  
  
All compilers give warning if it is used in user code:  
  
	#include <memory>  
  
	void f(char(*p)[5])  
	{  
		std::default_delete<char[5]> d;  
		d(p); // no warning in clang/gcc  
  
		delete p; // warning  
	}  
  
There is an ongoing discussion in LWG on whether a partial specialization should be added for 'T[N]'.  
But in the current state, we believe the warning is legit.

---

## Comment 1

> Username: ldionne  
> Created at: 2018-05-30 03:57:18 UTC  
> Url: https://github.com/boostorg/hana/issues/402#issuecomment-393022653  

Thanks for the report -- will fix.
