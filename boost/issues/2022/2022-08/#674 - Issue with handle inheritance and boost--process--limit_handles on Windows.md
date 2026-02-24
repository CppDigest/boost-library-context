# #674 - Issue with handle inheritance and boost::process::limit_handles on Windows [Open]

> Username: stavikpetr  
> Created at: 2022-08-08 12:10:06 UTC  
> Updated at: 2022-08-08 12:10:06 UTC  
> Url: https://github.com/boostorg/boost/issues/674  

Env  
OS: Windows  
Boost: 1.78.0  
  
Hello, I have discovered a (potential) issue with implementation of boost::process::limit_handles handler, that should be used when we want to limit handle inheritance in child processess. The current implementation works, as far as I have understood it, by first retrieving the currently open handles, and then by specifically marking those handles, that the child process should not inherit by setting the `HANDLE_FLAG_INHERIT` to `false`. However, I think that this is not correct, because if you open another file after the handles were retrieved, but before you launch the process, you essentially forget to mark one of the handles as not inheritable.  
  
The issue can be easily reproduced. First, set some sleep in function `limit_handles::on_setup()` like in the following code.  
```  
template<typename Executor>  
void on_setup(Executor & exec) const  
{  
    // ...  
  
    for (auto handle : handles_with_inherit_flag)  
        ::boost::winapi::SetHandleInformation(handle, ::boost::winapi::HANDLE_FLAG_INHERIT_, 0);  
  
    // this is the added line  
    std::this_thread::sleep_for(std::chrono::seconds(2));  
}  
```  
  
Then, prepare some simple code that will be used for the child process.  
  
```  
#include <iostream>  
#include <thread>  
#include <chrono>  
  
int main()  
{  
	std::cout << "Hello world from child process" << std::endl;  
	std::this_thread::sleep_for(std::chrono::seconds(5));  
	std::cout.flush();  
}  
```  
  
Lastly, create the main application that will be launching the previous code.  
```  
#include <thread>  
#include <chrono>  
#include <string>  
#include <future>  
#include <fstream>  
#include <filesystem>  
#include <boost/process/async.hpp>  
#include <boost/process/group.hpp>  
#include <boost/process/child.hpp>  
#include <boost/process/io.hpp>  
#include <boost/asio/io_service.hpp>  
#include <boost/process/handles.hpp>  
  
std::string callProcess(const std::string& exePath)  
{  
	boost::asio::io_service ios;  
	std::future<std::string> out;  
	auto childProcess = boost::process::child(  
		exePath,  
		boost::process::std_in.close(),  
		boost::process::std_out > out,  
		ios,  
		boost::process::limit_handles);  
  
	ios.run();  
  
	auto childOut = out.get();  
  
	return childOut;  
}  
  
  
int main()  
{  
	// create process in a new thread   
	std::thread t([&]() {  
		callProcess("C:\\code\\pg\\HelloWorldSleep\\x64\\Debug\\HelloWorldSleep.exe");  
	});  
  
	// wait for a bit so that we are sure that the other thread is sleeping in the  
	// limit_handles::on_setup() function  
	std::this_thread::sleep_for(std::chrono::seconds(1));  
  
	// open some file - this handle was not included in the handles that were specifically  
	// marked as not for inheritnace in limit_handles::on_setup() function  
	std::fstream fs("file.txt", std::fstream::in | std::fstream::out | std::fstream::trunc);  
  
	// wait for a bit - be sure that the process was already launched and it has the handle  
	std::this_thread::sleep_for(std::chrono::seconds(3));  
  
	// close the file and try to remove - this will throw exception because the  
	// child process inherited the open handle  
	fs.close();  
	try {  
		std::filesystem::remove("file.txt");  
	}  
	catch (const std::exception& e) {  
		// caught - kaboom  
	}  
  
	t.join();	  
}  
```  
  
I do not know if this is the intended implementation, but it was the source of some nasty bugs in one of the servers in our company. I have a workaround. The basic idea that prevents this issue is that instead of marking the handles that should not be inherited, we mark the handles that should be inherited. The code looks something like this:  
  
```  
struct LimitHandles : boost::process::detail::handler {  
    std::unique_ptr<System::AttributeList> attList;  
    std::vector<boost::winapi::HANDLE_> handlesToInherit;  
  
    template <typename Executor>  
    void on_setup(Executor& exec)  
    {  
        if (exec.startup_info.hStdOutput != boost::winapi::invalid_handle_value) {  
            handlesToInherit.push_back(exec.startup_info.hStdOutput);  
        }  
        if (exec.startup_info.hStdInput != boost::winapi::invalid_handle_value) {  
            handlesToInherit.push_back(exec.startup_info.hStdInput);  
        }  
        if (exec.startup_info.hStdError != boost::winapi::invalid_handle_value) {  
            handlesToInherit.push_back(exec.startup_info.hStdError);  
        }  
  
        // create an attribute list from a list of handles; this is our internal wrapper  
        // around LPPROC_THREAD_ATTRIBUTE_LIST  
        attList = std::make_unique<System::AttributeList>(handlesToInherit);  
  
        exec.set_startup_info_ex();  
        exec.startup_info_ex.lpAttributeList = attList->get();  
    }  
};  
```  
  
And this struct can then be used as the argument instead of `boost::process::limit_handles`. I am not sure if this is the best solution, or if it does not have any pitfalls, but it seems to fix the issue with some random dangling open file handle in some child process which causes issues when you want to remove the file.
