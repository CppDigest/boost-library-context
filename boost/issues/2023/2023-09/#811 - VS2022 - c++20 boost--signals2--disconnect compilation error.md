# #811 - VS2022 / c++20 boost::signals2::disconnect compilation error [Closed]

> Username: laurentcau  
> Created at: 2023-09-26 10:16:45 UTC  
> Updated at: 2023-09-26 10:21:17 UTC  
> Closed at: 2023-09-26 10:21:16 UTC  
> Url: https://github.com/boostorg/boost/issues/811  

Hello,  
  
We're upgrading our c++ projects to Visual studio 2022 / c++20 and we have an compiler error with boost::signals2::signal::disconnect + boost::bind.  
...\include\boost\signals2\detail\signal_template.hpp(530,46): error C2088: '==': illegal for class  
Here is an example:  
```  
class CTest  
{  
public:  
       boost::signals2::signal<void()> mySignal;  
	CTest()  
	{  
		mySignal.connect(boost::bind(&CTest::Method, this));  
		mySignal.disconnect(boost::bind(&CTest::Method, this)); //=> generates error with VS 2022 c++20, not with c++17  
	}  
  
	void Method()  
	{}  
};  
```  
Is there a workaround for this ?  
Thanks  
Regards,  
Laurent

---

## Comment 1

> Username: laurentcau  
> Created at: 2023-09-26 10:21:16 UTC  
> Url: https://github.com/boostorg/boost/issues/811#issuecomment-1735253171  

Sorry, duplicate.  
I didn't see my previous message was transfered.
