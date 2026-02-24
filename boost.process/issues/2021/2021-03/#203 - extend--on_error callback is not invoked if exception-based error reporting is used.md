# #203 - extend::on_error callback is not invoked if exception-based error reporting is used [Open]

> Username: tomix86  
> Created at: 2021-03-16 09:47:56 UTC  
> Updated at: 2021-03-16 09:47:56 UTC  
> Url: https://github.com/boostorg/process/issues/203  

[extend::on_error](https://www.boost.org/doc/libs/1_75_0/doc/html/boost/process/extend/on_error.html) is not invoked when `exec()` (POSIX) or `CreateProcess()` (Windows) fails.  
This is because `set_error()` called after `exec()` ([[1]](https://github.com/boostorg/process/blob/develop/include/boost/process/detail/posix/executor.hpp#L433), [[2]](https://github.com/boostorg/process/blob/develop/include/boost/process/detail/posix/executor.hpp#L265)) / `CreateProcess()`  ([[1]](https://github.com/boostorg/process/blob/develop/include/boost/process/detail/windows/executor.hpp#L210)) failure throws an exception and therefore subsequent code block is not executed, causing `on_error` handler to be omitted.  
  
A minimal reproducer:  
```  
#include <gmock/gmock.h>  
#include <gtest/gtest.h>  
#include <boost/process.hpp>  
#include <boost/process/extend.hpp>  
  
namespace bp = boost::process;  
  
TEST(BoostProcessTest, on_error) {  
	{  
		bool called{false};  
  
		std::error_code ec;  
		bp::child{bp::exe = "some_nonexistent_path", ec, bp::extend::on_error = [&](auto, auto) { called = true; }}.wait();  
		EXPECT_TRUE(called);  
	}  
  
	{  
		bool called{false};  
  
		try {  
			bp::child{bp::exe = "some_nonexistent_path", bp::extend::on_error = [&](auto, auto) { called = true; }}.wait();  
		} catch (...) {  
		}  
  
		// EXPECT_TRUE(called);  
		EXPECT_FALSE(called); // Boost.Process bug  
	}  
}  
  
int main(int argc, char** argv) {  
	::testing::InitGoogleTest(&argc, argv);  
	return RUN_ALL_TESTS();  
}  
```
