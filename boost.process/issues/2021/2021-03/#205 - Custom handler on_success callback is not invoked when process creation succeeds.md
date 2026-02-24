# #205 - Custom handler on_success callback is not invoked when process creation succeeds [Closed]

> Username: tomix86  
> Created at: 2021-03-24 12:59:53 UTC  
> Updated at: 2021-10-19 04:28:01 UTC  
> Closed at: 2021-10-19 04:28:00 UTC  
> Url: https://github.com/boostorg/process/issues/205  

When defining a [custom handler](https://www.boost.org/doc/libs/1_75_0/doc/html/boost_process/extend.html#boost_process.extend.handler), it's `on_success` callback is not invoked when process creation succeeds.  
  
A minimal reproducer:  
```  
#include <gmock/gmock.h>  
#include <gtest/gtest.h>  
#include <boost/process.hpp>  
#include <boost/process/extend.hpp>  
  
namespace bp = boost::process;  
  
struct CustomHandler : bp::extend::handler {  
	template <typename Executor>  
	void on_setup(Executor& executor) {  
		setupCalled = true;  
	}  
  
	template <typename Executor>  
	void on_error(Executor&, const std::error_code&) {  
		errorCalled = true;  
	}  
  
	template <typename Executor>  
	void on_sucess(Executor&) {  
		successCalled = true;  
	}  
  
	bool setupCalled{false};  
	bool errorCalled{false};  
	bool successCalled{false};  
};  
  
TEST(BoostProcessTest, custom_handler) {  
	{  
		CustomHandler handler;  
		std::error_code ec;  
		bp::child{bp::exe = "some_nonexistent_path", ec, handler}.wait();  
		EXPECT_TRUE(handler.setupCalled);  
		EXPECT_TRUE(handler.errorCalled);  
		EXPECT_FALSE(handler.successCalled);  
	}  
  
	{  
		CustomHandler handler;  
		std::error_code ec;  
#ifdef _WINDOWS  
		constexpr auto exe{R"(C:\Windows\System32\attrib.exe)"};  
#else  
		constexpr auto exe{"/bin/ls"};  
#endif  
		bp::child{bp::exe = exe, ec, handler}.wait();  
		EXPECT_TRUE(handler.setupCalled);  
		EXPECT_FALSE(handler.errorCalled);  
		// EXPECT_TRUE(handler.successCalled);  
		EXPECT_FALSE(handler.successCalled); // Boost.Process bug  
	}  
}  
  
int main(int argc, char** argv) {  
	::testing::InitGoogleTest(&argc, argv);  
	return RUN_ALL_TESTS();  
}  
```

---

## Comment 1

> Username: garethsb  
> Created at: 2021-10-18 14:29:33 UTC  
> Url: https://github.com/boostorg/process/issues/205#issuecomment-945835744  

Example has spelling mistake which may cause this... `on_sucess` should be `on_success`...

---

## Comment 2

> Username: tomix86  
> Created at: 2021-10-19 04:28:00 UTC  
> Url: https://github.com/boostorg/process/issues/205#issuecomment-946357462  

Thanks a lot @garethsb! This was indeed the culprit, with the typo fixed the problem no longer occurs.
