# #66 - Multiple definitions while linking [Closed]

> Username: harrywong  
> Created at: 2018-12-17 15:41:41 UTC  
> Updated at: 2019-08-25 07:49:35 UTC  
> Closed at: 2018-12-17 20:00:11 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/66  

The `make_error_code` function in `exception.hpp` is not made `inline`. So when include `safe_numerics` in multiple source files, it will turned into link error with duplicate symbols.

---

## Comment 1

> Username: robertramey  
> Created at: 2018-12-17 20:00:06 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/66#issuecomment-447978716  

I investigate this - see https://akrzemi1.wordpress.com/2014/07/14/inline-functions/ and I'm still not sure why this comes up.  But in any case, the function is unused from within the library and not documented as part of the API so I've fixed this by commenting out the unused function.

---

## Comment 2

> Username: harrywong  
> Created at: 2018-12-18 02:12:57 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/66#issuecomment-448070883  

Thanks, currently I used this workaround: add include one duplicate `exception.hpp` before include `safe_numerics` library. And I make those two functions (`make_error_code` and `make_error_condition`) as inline functions.  
  
BTW. I tried to remove these two functions, it will turn into compile errors.

---

## Comment 3

> Username: wwqq46954  
> Created at: 2019-08-25 07:49:35 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/66#issuecomment-524609238  

Placing this before including boost headers worked for me:  
  
```  
// prevent multiple definitions of make_error_code  
namespace boost::safe_numerics {  
	enum class safe_numerics_error : std::uint8_t;  
	inline std::error_code make_error_code(const ::boost::safe_numerics::safe_numerics_error& );  
}  
```
