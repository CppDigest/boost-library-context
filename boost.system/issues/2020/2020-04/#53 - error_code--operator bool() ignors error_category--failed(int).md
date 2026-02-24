# #53 - error_code::operator bool() ignors error_category::failed(int) [Closed]

> Username: Zombie-3000  
> Created at: 2020-04-09 14:29:41 UTC  
> Updated at: 2020-06-16 21:08:39 UTC  
> Closed at: 2020-06-16 21:08:39 UTC  
> Url: https://github.com/boostorg/system/issues/53  

Hi,  
  
error_code::operator bool() returns true/false based on the raw numeric value of the error code, instead of if this value is actually representing an error (like reported from error_category::failed(int)). I know, it matches the description in the [reference doc](https://www.boost.org/doc/libs/1_72_0/libs/system/doc/html/system.html#ref_class_error_code), where it says "returns val_ != 0", but to me, it's at least not, what **I** would expect. It's pretty useless, isn't it?  
  
Any opinions?  
  
best regards  
  
code:  
```  
#include <cassert>  
#include <boost/system/error_code.hpp>  
  
class TestErrorCategory : public boost::system::error_category {  
public:  
	virtual const char *name() const noexcept override {  
		return "test error category";  
	}  
  
	virtual std::string message(int ev) const override {  
		switch (ev) {  
		case 0:  
			return "error";  
  
		case 1:  
			return "ok";  
  
		default:  
			assert(false);  
			return "";  
		}  
	}  
  
	virtual bool failed(int ev) const noexcept override {  
		return ev != 1;  
	}  
};  
  
int main() {  
	{  
		boost::system::error_code test_error(boost::system::errc::make_error_code(boost::system::errc::operation_canceled));  
		assert(static_cast<bool>(test_error) == test_error.failed());  
		assert(test_error.failed());  
		assert(test_error);  
	}  
	{  
		boost::system::error_code test_error;  
		assert(static_cast<bool>(test_error) == test_error.failed());  
		assert(!test_error.failed());  
		assert(!test_error);  
	}  
	{  
		TestErrorCategory test_error_category;  
		boost::system::error_code test_error(1, test_error_category); // represents no error  
		assert(static_cast<bool>(test_error) == test_error.failed()); // assertion fails  
		assert(!test_error.failed());  
		assert(!test_error); // assertion fails  
	}  
	{  
		TestErrorCategory test_error_category;  
		boost::system::error_code test_error(0, test_error_category); // represents error  
		assert(static_cast<bool>(test_error) == test_error.failed()); // assertion fails  
		assert(test_error.failed());  
		assert(test_error); // assertion fails  
	}  
}  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2020-04-09 17:56:19 UTC  
> Url: https://github.com/boostorg/system/issues/53#issuecomment-611666138  

Returning `value() != 0` is the original behavior of `operator bool`, which is also what's in C++11. At the time I added `failed` (with the intent of proposing the same change for the C++ standard) I decided to not change `operator bool` in order to preserve backward compatibility; the idea was that new code should use the more explicit `if(ec.failed())` anyway, as it unambiguously signifies that `ec` is tested for failure (whereas `if(ec)` isn't as clear.)  
  
However, the proposed change to `std::error_code` was rejected, so the argument of maintaining compatibility with the standard no longer holds. It might indeed make sense to change `operator bool` to call `failed`.  
  
Boost 1.73 is now beta and closed for changes, but I'll consider this for 1.74.

---

## Comment 2

> Username: pdimov  
> Created at: 2020-06-16 21:08:39 UTC  
> Url: https://github.com/boostorg/system/issues/53#issuecomment-645012944  

The change has been implemented on the develop branch and will be part of 1.74.
