# #42 - vs2019 bulid error .. [Closed]

> Username: bilbilemm  
> Created at: 2019-10-03 18:56:06 UTC  
> Updated at: 2019-11-20 16:49:51 UTC  
> Closed at: 2019-11-20 16:49:51 UTC  
> Url: https://github.com/boostorg/pfr/issues/42  

Visual Studio 2019 (v142)  
error C2338: ====================> Boost.PFR: Types with user specified constructors (non-aggregate initializable types) are not supported.  
查看对正在编译的函数 模板 实例化“size_t boost::pfr::detail::fields_count<Type>(void) noexcept”的引用  
------------------------------------------------------------------------------------------------------  
struct msg_HelperStr {  
	HelperStrType	_HelperType;  
	std::string		_HelperStr;  
};

---

## Comment 1

> Username: bilbilemm  
> Created at: 2019-10-03 18:56:47 UTC  
> Url: https://github.com/boostorg/pfr/issues/42#issuecomment-538079140  

enum HelperStrType {  
	helper_normal,					//正常  
	helper_error,					//异常  
};

---

## Comment 2

> Username: apolukhin  
> Created at: 2019-11-11 18:10:31 UTC  
> Url: https://github.com/boostorg/pfr/issues/42#issuecomment-552552175  

With what /std flag are you building? /std:c++17 ?

---

## Comment 3

> Username: spudwa  
> Created at: 2019-11-12 10:30:01 UTC  
> Url: https://github.com/boostorg/pfr/issues/42#issuecomment-552833372  

If your using VS2019 it supports spaceship operator :-)  
https://devblogs.microsoft.com/cppblog/simplify-your-code-with-rocket-science-c20s-spaceship-operator/

---

## Comment 4

> Username: bilbilemm  
> Created at: 2019-11-20 16:47:23 UTC  
> Url: https://github.com/boostorg/pfr/issues/42#issuecomment-556094108  

> 使用什么/ std标志构建？/ std：c ++ 17？  
  
   
  
> With what /std flag are you building? /std:c++17 ?  
Sorry.. I used it. /std:c++latest。Will you update this project? This project is very good,Gave me a lot of help。 thank you
