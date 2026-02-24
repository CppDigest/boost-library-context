# #216 - Zombie process on Linux running non existing process [Open]

> Username: aleksandarvucetic  
> Created at: 2021-08-13 12:36:45 UTC  
> Updated at: 2022-08-03 08:14:37 UTC  
> Url: https://github.com/boostorg/process/issues/216  

Hello,  
  
A program like this:  
  
```cpp  
#include <boost/version.hpp>  
#include <boost/process/child.hpp>  
#include <boost/process/io.hpp>  
#include <boost/process/pipe.hpp>  
#include <string>  
#include <sstream>  
#include <iostream>  
  
void runProcess(const std::string& arg_command)  
{  
    try  
    {  
        boost::process::ipstream out, err; // reading pipe-stream  
        boost::process::child process(arg_command, boost::process::std_out > out, boost::process::std_err > err);  
        process.wait(); // wait for the process to exit  
        int result = process.exit_code();  
  
        std::stringstream output, error;  
        out >> output.rdbuf();  
        err >> error.rdbuf();  
        if(result == 0)  
        {  
            std::cout << output.str() << "\n";  
        }  
        else  
        {  
            std::cout << "Error output: " << output.str() << "\n" <<  error.str() << "\n";  
        }  
    }  
    catch(const std::system_error& e)  
    {  
        std::string err_msg = std::string("Exception: ") + e.what();  
  
        std::cerr << err_msg << "\n";  
    };  
}  
  
int main()  
{  
    for (int i=0; i<50; i++)  
    {  
        runProcess("/usr/bin/does_not_exist");  
    }  
  
    std::cout << "Boost version: " << BOOST_LIB_VERSION << "\n";  
    std::cout << "Open another shell and check for zombie process..." << "\n";  
    int val = 0;  
    std::cin >> val;  
}  
```  
  
I got 50 zombie processes.  
  
Tested on Ubuntu 20.04, g++ 9.3, boost versions 1.71 and 1.75 exhibit this behavior.  
  
More importantly, boost 1.72 running on funky old ARMv5 based embedded Linuxes, our bread and butter, same thing.  
Poor machines eventually got "Too many files opened" after months of being online.  
  
Q: ok, so the executable does not exist, exception handler is invoked, that is understood, but how is it that child process is now zombied? There should not be any child process in the first place as file simply does not exist. Is `fork()` performed before checking if executable exists?

---

## Comment 1

> Username: wasphin  
> Created at: 2022-06-09 03:24:27 UTC  
> Url: https://github.com/boostorg/process/issues/216#issuecomment-1150626529  

You may need to enable the `boost::process::ignore_error` option.

---

## Comment 2

> Username: vasirotn  
> Created at: 2022-08-03 08:12:06 UTC  
> Updated at: 2022-08-03 08:14:37 UTC  
> Url: https://github.com/boostorg/process/issues/216#issuecomment-1203629084  

The issue here is that the process creation is not atomic, but the fact the `exec` step fails, leads to `child` being invalid, anbd skipping the `wait` on the process (it also sets pid to 01, which makes waiting on it manually tricky).  
  
The way you can handle this, is add an `on_error` extension to the constructor like so  
  
```cpp  
auto child = boost::process::child(  
	binary_name,   
	boost::process::args(args),   
	...  
	boost::process::extend::on_error  = [] (auto & exec , auto /* ec */ )   
	{  
		// if we get an error during fork/exec, boost::proc::child does not collect the forked process  
		// and we can't handle it from surrounding code, since we don't have a pid of the failed child.  
		// so we end up leaking a zombie process.  
  
		int status;  
		::waitpid(exec.pid, &status, 0);  
	} ,  
	...  
	);  
```  
Consider adding some timeout logic and calling `waitpid` with `WNOHANG` instead, to avoid freak hangs.  
Also might want to check `exec.pid` is not 0 (it is documented it could also be called in the target proc, which will hang it)  
  
I'm not familiar enough with the implementation to identify where is the correct place to fix this in PR.
