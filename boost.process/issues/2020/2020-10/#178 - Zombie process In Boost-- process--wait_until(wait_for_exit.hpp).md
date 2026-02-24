# #178 - Zombie process In Boost:: process::wait_until(wait_for_exit.hpp) [Open]

> Username: amritanshujohri  
> Created at: 2020-10-12 20:34:34 UTC  
> Updated at: 2022-08-02 20:31:01 UTC  
> Url: https://github.com/boostorg/process/issues/178  

I have observed Z processes for MacOSX when we use Boost::Process::wait_until(), I see it on 1.73, but the offending code exists in 1.74 as well.   
  
```  
// child_cleaner is a class responsible for cleaning up the fork() used to simulate sigtimedwait()  
  
 struct child_cleaner_t  
    {  
        pid_t pid;  
        ~child_cleaner_t()  
        {  
            int res;  
            ::kill(pid, SIGKILL);   
            ::waitpid(pid, &res, WNOHANG); <-- wait with WNOHANG can result in process leak as the kill is async   
// for short-run cmd that terminate quickly there is a clear repro. If it were up to me, I would remove WNOHANG here as the child process will eventually terminate. or at least queue a wait in a cleanup thread.  
        }  
    };  
  
```  
  
Steps to repro:  
```  
  
const auto& cmdLine = boost::str(boost::format("/usr/bin/time"));  
	for(int i = 0; i <100; i++)  
	{  
		boost::process::child x(cmdLine, boost::process::std_out > boost::process::null, boost::process::std_err > boost::process::null);  
		x.wait_for(std::chrono::seconds{10});  
	}  
```  
I hope this is the best way to approach the community if you think I should talk to some DL or group elsewhere then do let me know.

---

## Comment 1

> Username: 22hemantgupta  
> Created at: 2020-11-03 08:28:01 UTC  
> Url: https://github.com/boostorg/process/issues/178#issuecomment-720973833  

I am beginner, so can you please give me some hints on where to start and how to start to resolve this issue.

---

## Comment 2

> Username: robbert-vdh  
> Created at: 2021-01-27 19:54:55 UTC  
> Url: https://github.com/boostorg/process/issues/178#issuecomment-768537972  

It's also impossible to prevent zombies when the child process has already exited, because [`boost::process::child::wait()`](https://github.com/boostorg/process/blob/8222a57744a5df9beddcc7bddc168af66c85e3c7/include/boost/process/detail/child_decl.hpp#L172) will only allow you to wait on the child when the process is still running.

---

## Comment 3

> Username: anthonybrandon  
> Created at: 2022-08-02 20:31:00 UTC  
> Url: https://github.com/boostorg/process/issues/178#issuecomment-1203184593  

I just ran into this or at least a similar issue:  
```cpp  
try {  
   child c("foo");  
   c.wait();  
} catch (...) {  
}  
```  
where `foo` does not exist will result in a zombie process.
