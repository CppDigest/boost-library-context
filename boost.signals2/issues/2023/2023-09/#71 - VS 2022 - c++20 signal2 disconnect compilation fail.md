# #71 - VS 2022 / c++20 signal2 disconnect compilation fail [Closed]

> Username: laurentcau  
> Created at: 2023-09-25 13:20:15 UTC  
> Updated at: 2024-05-31 13:58:41 UTC  
> Closed at: 2024-05-31 13:58:41 UTC  
> Url: https://github.com/boostorg/signals2/issues/71  

Hi,  
  
We have a compilation issue with VS 2022 / c++20. It compiles fine with c++17.  
This code doesn't compile anymore:  
```  
class CTest  
{  
public:  
	CTest()  
	{  
		boost::signals2::signal<void()> mySignal;  
		mySignal.connect(boost::bind(&CTest::Method, this));  
		mySignal.disconnect(boost::bind(&CTest::Method, this));  
	}  
  
	void Method()  
	{}  
};  
```  
Is there any workaround ?

---

## Comment 1

> Username: fmhess  
> Created at: 2023-09-29 16:49:12 UTC  
> Url: https://github.com/boostorg/signals2/issues/71#issuecomment-1741202716  

you could use the develop versions of boost.function and boost.signals2, or disconnect using the connection object returned by the connect call.
