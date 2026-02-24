# #202 - limit_handles breaks exec() error reporting on POSIX [Closed]

> Username: tomix86  
> Created at: 2021-03-16 09:19:53 UTC  
> Updated at: 2024-02-20 10:02:42 UTC  
> Closed at: 2022-06-11 07:00:37 UTC  
> Url: https://github.com/boostorg/process/issues/202  

Using [limit_handles](https://www.boost.org/doc/libs/1_75_0/doc/html/boost/process/limit_handles.html) on POSIX platforms causes internal logic for `exec()` error reporting to break.  
The root cause is that [foreach_used_handle](https://github.com/boostorg/process/blob/develop/include/boost/process/detail/used_handles.hpp#L62) on POSIX does not account for [executor::_pipe_sink](https://github.com/boostorg/process/blob/develop/include/boost/process/detail/posix/executor.hpp#L150), which is needed for error reporting from child to parent.  
  
A minimal reproducer:  
```  
#include <gmock/gmock.h>  
#include <gtest/gtest.h>  
#include <boost/process.hpp>  
#include <boost/process/extend.hpp>  
  
namespace bp = boost::process;  
  
TEST(BoostProcessTest, limit_handles_breaks_exec_error_handling) {  
	{  
		std::error_code ec;  
		bp::child{bp::exe = "some_nonexistent_path", ec}.wait();  
		EXPECT_EQ(std::errc::no_such_file_or_directory, ec);  
	}  
  
	{  
		std::error_code ec;  
		bp::child{bp::exe = "some_nonexistent_path", ec, bp::limit_handles}.wait();  
		// EXPECT_EQ(std::errc::no_such_file_or_directory, ec);  
		EXPECT_EQ(std::error_code{}, ec); // Boost.Process bug  
	}  
}  
  
int main(int argc, char** argv) {  
	::testing::InitGoogleTest(&argc, argv);  
	return RUN_ALL_TESTS();  
}  
```

---

## Comment 1

> Username: sfc-gh-hyu  
> Created at: 2022-06-09 22:03:04 UTC  
> Url: https://github.com/boostorg/process/issues/202#issuecomment-1151666058  

Got the same error, is there any update on this? Or at least small patch on how to fix this?

---

## Comment 2

> Username: dkl  
> Created at: 2024-02-20 10:01:50 UTC  
> Updated at: 2024-02-20 10:02:42 UTC  
> Url: https://github.com/boostorg/process/issues/202#issuecomment-1953862870  

Testing with boost-process 1.84.0, this issue still seems to exist. Commit 1a1d677d769f4016422c0b26de971f43ec91a20f added `executor::get_used_handles()`, but it doesn't work, because `_pipe_sink` is assigned after the `on_setup()` call, so it's missed and closed anyways.  
  
```c++  
#include <boost/process/handles.hpp>  
#include <boost/process/system.hpp>  
int main() {  
	// with limit_handles: no exception thrown, but should be  
	// without limit_handles: throws exception as expected  
	boost::process::system("/does/not/exist", boost::process::limit_handles);  
	return 0;  
}  
```
