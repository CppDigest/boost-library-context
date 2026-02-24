# #257 - Include guard names collision [Closed]

> Username: Enhex  
> Created at: 2017-11-02 11:42:20 UTC  
> Updated at: 2017-12-06 10:51:54 UTC  
> Closed at: 2017-12-06 10:51:54 UTC  
> Url: https://github.com/boostorg/spirit/issues/257  

I'm getting this error when trying to use karma in boost 1.65.1:  
`'signbit': is not a member of 'boost::spirit::detail'`  
  
It seems that `SPIRIT_SIGN_MAR_11_2009_0734PM` is already defined in:  
`boost\spirit\home\x3\support\numeric_utils\sign.hpp`  
which triggers the include guard of:  
`boost\spirit\home\support\detail\sign.hpp`  
  
I can work around it by using this code:  
```C++  
namespace boost {  
	namespace spirit {  
		namespace detail {  
			using boost::spirit::x3::signbit;  
		}  
	}  
}  
```

---

## Comment 1

> Username: Kojoley  
> Created at: 2017-11-10 21:40:36 UTC  
> Url: https://github.com/boostorg/spirit/issues/257#issuecomment-343594672  

https://svn.boost.org/trac10/ticket/12084 is linked issue
