# #987 - Unable to extract boolean value in numberic format [Closed]

> Username: k-nero  
> Created at: 2024-03-01 12:51:55 UTC  
> Updated at: 2024-03-01 13:07:06 UTC  
> Closed at: 2024-03-01 13:07:06 UTC  
> Url: https://github.com/boostorg/json/issues/987  

Version of Boost  
1.84.0  
  
Code:  
```C++  
class X  
{  
public:  
	X() : x(false) {}  
	X(bool x) : x(x) {}  
protected:  
	bool x;  
	BOOST_DESCRIBE_CLASS(X, (), (), (x), ())  
};  
  
int main()  
{  
	try  
	{  
		boost::json::value jv = { { "x", 0 } };  
		auto a = boost::json::value_to<X>(jv);  
	}  
	catch (std::exception const& e)  
	{  
		std::cerr << "Error: " << e.what() << std::endl;  
	}  
}  
```  
This would faild and throw an exception: Error: value is not boolean

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-03-01 13:01:07 UTC  
> Url: https://github.com/boostorg/json/issues/987#issuecomment-1973156856  

Yes. This is by design. 0 is not a boolean value.
