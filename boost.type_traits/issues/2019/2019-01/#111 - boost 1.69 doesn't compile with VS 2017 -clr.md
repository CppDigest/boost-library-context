# #111 - boost 1.69 doesn't compile with  VS 2017 /clr [Closed]

> Username: BoostBugReporter  
> Created at: 2019-01-15 13:27:35 UTC  
> Updated at: 2019-02-20 13:40:52 UTC  
> Closed at: 2019-02-20 13:40:52 UTC  
> Url: https://github.com/boostorg/type_traits/issues/111  

[BoostConsole.zip](https://github.com/boostorg/boost/files/2759777/BoostConsole.zip)  
  
If you try to compile this with  VS 2017 15.9.5 using boost 1.69 you get 60 errors and 48 warnings  
(It does compile with boost 1.68)  
  
Error	C2764	  
     'C': template parameter not used or deducible in partial specialization 'boost::is_member_function_pointer<Ret(Args...)>'	  
     boost\type_traits\detail\is_member_function_pointer_cxx_11.hpp	114	  
  
Warning	C4561  
	'__fastcall' incompatible with the '/clr' option: converting to '__stdcall'  
	boost\type_traits\detail\is_member_function_pointer_cxx_11.hpp	120	  
  
Error	C2953  
	'boost::is_member_function_pointer<Ret(__stdcall C::* )(Args...)>': class template has already been defined	  
	boost\type_traits\detail\is_member_function_pointer_cxx_11.hpp	120	  
...  
  
with best regards  
  
  Hendrik

---

## Comment 1

> Username: rcdailey  
> Created at: 2019-02-19 22:06:13 UTC  
> Url: https://github.com/boostorg/type_traits/issues/111#issuecomment-465330238  

I'm hitting this as well. No feedback from Boost devs?

---

## Comment 2

> Username: jzmaddock  
> Created at: 2019-02-20 13:40:52 UTC  
> Url: https://github.com/boostorg/type_traits/issues/111#issuecomment-465577728  

Sorry, must have missed this one - it's a duplicate of https://github.com/boostorg/type_traits/issues/99 and the linked commits from there contain the necessary fixes.
