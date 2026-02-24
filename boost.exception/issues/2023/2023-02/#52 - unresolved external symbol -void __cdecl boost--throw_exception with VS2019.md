# #52 - unresolved external symbol "void __cdecl boost::throw_exception with VS2019 [Closed]

> Username: sthalasayanam  
> Created at: 2023-02-20 16:17:49 UTC  
> Updated at: 2023-02-21 22:03:19 UTC  
> Closed at: 2023-02-21 22:03:18 UTC  
> Url: https://github.com/boostorg/exception/issues/52  

I get the compiler error unresolved external symbol "void __cdecl boost::throw_exception in VS2019 even if I have the custom function  
  
namespace boost{  
#ifdef BOOST_NO_EXCEPTIONS  
	template<class E>  
	void throw_exception(E const& e) {  
	//do nothing  
}  
#endif  
}

---

## Comment 1

> Username: zajo  
> Created at: 2023-02-21 22:03:18 UTC  
> Url: https://github.com/boostorg/exception/issues/52#issuecomment-1439148449  

There are two issues with your function:  
  
First, it may not be a template. It should just take a `std::exception const &`.  
  
Secondly, it is not allowed to return, so can't "do nothing". Call terminate or throw an exception, those are your choices.
