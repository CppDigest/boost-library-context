# #217 - boost::process:: child::wait() on Linux ignores a zombie process [Open]

> Username: theroyn  
> Created at: 2021-08-19 15:12:15 UTC  
> Updated at: 2025-12-11 10:24:43 UTC  
> Url: https://github.com/boostorg/process/issues/217  

Reproducible in the following example:  
```  
#include <boost/process.hpp>  
  
int main()  
{  
        namespace bp = boost::process;  
  
        std::string appCommand = "sleep 50";  
        bp::child childProcess(appCommand);  
  
        int pid = childProcess.id();  
  
        printf("childProcess.running()=%d\n", childProcess.running());  
  
        childProcess.terminate();  
        childProcess.wait();  
  
        printf("childProcess.running()=%d\n", childProcess.running());  
  
        // kill 0 returns 0 if process is running  
        bool isRunning = (::kill(pid, 0) == 0);  
  
        printf("isRunning=%d\n", isRunning);  
}  
```  
output on my machine is:  
```  
childProcess.running()=1  
childProcess.running()=0  
(kill 0 23764): isRunning=1  
```  
The program will print ```isRunning=1``` at its end, meaning that the process is still running. Pausing at that point with debugger and checking ```ps -ax``` will also show that the process is a zombie.  
  
After some investigation, faulty code seem to be in ```include/boost/process/detail/child_decl.hpp```:  
```  
    bool _exited()  
    {  
        return _terminated || !::boost::process::detail::api::is_running(_exit_status->load());  
    };  
  
    void terminate(std::error_code & ec) noexcept  
    {  
        if (valid() && running(ec) && !ec)  
            boost::process::detail::api::terminate(_child_handle, ec);  
  
        if (!ec)  
            _terminated = true;  
    }  
  
    void wait(std::error_code & ec) noexcept  
    {  
        if (!_exited() && valid())  
        {  
            int exit_code = 0;  
            boost::process::detail::api::wait(_child_handle, exit_code, ec);  
            if (!ec)  
                _exit_status->store(exit_code);  
        }  
    }  
```  
```terminate()``` marks ``` _terminated = true```, and ```wait()``` never calls its main logic because ```_exited()``` evaluates true.  
  
A possible solution would be to perform a blocking wait at the end of ```boost::process::detail::api::terminate()```, because ATM it waits with ```WNOHANG```.

---

## Comment 1

> Username: SilverPlate3  
> Created at: 2023-07-06 06:31:14 UTC  
> Url: https://github.com/boostorg/process/issues/217#issuecomment-1623070160  

@klemens-morgenstern   
Do you want me to create a pull request and fix this issue?

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2023-07-06 06:34:37 UTC  
> Url: https://github.com/boostorg/process/issues/217#issuecomment-1623073301  

I think I fixed this, maybe add a test for it?

---

## Comment 3

> Username: jmccabe  
> Created at: 2025-12-11 10:24:43 UTC  
> Url: https://github.com/boostorg/process/issues/217#issuecomment-3641251928  

Should this be closed and, if so, has it been fixed at some point or just replaced? child_decl.hpp in the now v1 directory on the develop branch hasn't had any updates to do anything about this.
