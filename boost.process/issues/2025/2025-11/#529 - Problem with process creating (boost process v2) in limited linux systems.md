# #529 - Problem with process creating (boost process v2) in limited linux systems [Open]

> Username: Sene4kass  
> Created at: 2025-11-27 11:20:50 UTC  
> Updated at: 2025-12-22 18:07:03 UTC  
> Url: https://github.com/boostorg/process/issues/529  

Hello!  
I'm using boost::process::v2 (boost version 1.89 - latest stable) in my project with cross-compiling the final binary. If I use the amd64 standard debian 12, all is ok and process creating ended successfully without errors. But if I using it in limited linux (system with busybox, specific process architecture (for example, armv7l)) I getting an unknown exception. Try/catch can't process that exception. Google breakpad also can't give me details.  
I checked the system - clone(), execve(), fork() works there. But bp::v2::process give me an unknown exception.  
Also I testing on aarch64 (arm64) limited system and on standard linux version system. In standard linux that works great, but on limited system I've got the same error.  
**Warning**: the "limited" version I mean Linux core + standard utilities such as busybox, libc, sh and other configurated extremely necessary utils.  
I tested work directory access - all is ok on my side. I running the "/bin/echo" manually from shell, and that works good.   
  
System info:  
armv7l, linux version 4.19.91,  
GNU C Library (Buildroot) stable release version 2.29.  
BusyBox v1.24.1 (2024-03-14 16:36:28 CST) multi-call binary  
  
Binary compiled with GCC 8.3.0  
  
Error log:  
  
```shell  
Creating process...  
terminate called after throwing an instance of 'boost::wrapexcept<boost::system::system_error>'  
terminate called recursively  
Aborted (core dumped)  
```   
  
Code:  
  
```c++  
#include <boost/process/v2/process.hpp>  
#include <boost/process/v2/stdio.hpp>  
#include <boost/program_options/errors.hpp>  
  
#include <iostream>  
  
int main()  
{  
    try {  
        boost::asio::io_context context;  
        std::cout << "Creating process..." << std::endl;  
        boost::process::v2::process proc(context, "/bin/echo", {"hello", "world!"}, boost::process::v2::process_stdio {});  
        std::cout << "Process was created." << std::endl;  
        context.run();  
  
        return EXIT_SUCCESS;  
  
    } catch (const boost::program_options::error& ex) {  
        std::cout << "Bad CLI: " << ex.what() << std::endl;  
    } catch (const boost::system::system_error& error) {  
        std::cout << "Exception: " << error.what() << ". Error code: " << error.code() << std::endl;  
    } catch (...) {  
        std::cout << "Unknown exception has occured." << std::endl;  
    }  
  
    return EXIT_FAILURE;  
}  
  
```

---

## Comment 1

> Username: Sene4kass  
> Created at: 2025-11-27 11:44:00 UTC  
> Url: https://github.com/boostorg/process/issues/529#issuecomment-3585417323  

**Important note**: on that system binary works with boost::process::v1, after upgrading appeared troubles

---

## Comment 2

> Username: Sene4kass  
> Created at: 2025-11-27 12:55:07 UTC  
> Updated at: 2025-11-27 12:59:18 UTC  
> Url: https://github.com/boostorg/process/issues/529#issuecomment-3585718897  

Testing on aarch64 show that result:  
```shell  
Creating process...  
Exception: default_launcher: No such file or directory [system:2 at /boost_1_87_0/include/boost/process/v2/posix/default_launcher.hpp:431 in function 'operator()']. Error code: system:2  
```   
I checked that file (/bin/echo) is exists and runs manually  
But linux version is 5.10.61

---

## Comment 3

> Username: AJIOB  
> Created at: 2025-12-21 13:26:54 UTC  
> Updated at: 2025-12-22 04:41:34 UTC  
> Url: https://github.com/boostorg/process/issues/529#issuecomment-3678795446  

After testing locally with boost 1.89, I found, that if I add some printf calls in the child process (inside the `if (child_pid == 0) {...}` block) - it started to work.  
  
IMO, it looks like as a data race.  
@klemens-morgenstern , do we have some better way to mitigate that?  
  
P.S. File not found errno value, that was reported in the @Sene4kass 's logs looks like the pipe related, not the executable one.  
P.P.S. My changeset:  
```cxx  
            else if (pid == 0)  
            {  
                ::close(pg.p[0]);  
#if !defined(BOOST_PROCESS_V2_DISABLE_NOTIFY_FORK)  
                ctx.notify_fork(net::execution_context::fork_child);  
#endif  
                printf("1\n");  
                ec = detail::on_exec_setup(*this, executable, argv, inits...);  
                printf("2: %d\n", ec);  
                if (!ec)  
                {  
                    printf("3\n");  
                    close_all_fds(ec);  
                }  
                if (!ec)  
                    printf("4\n");  
                    ::execve(executable.c_str(), const_cast<char * const *>(argv), const_cast<char * const *>(env));  
  
                printf("5: %d\n", ec);  
  
                ignore_unused(::write(pg.p[1], &errno, sizeof(int)));  
                BOOST_PROCESS_V2_ASSIGN_EC(ec, errno, system_category());  
                detail::on_exec_error(*this, executable, argv, ec, inits...);  
                ::exit(EXIT_FAILURE);  
                return basic_process<Executor>{exec};  
            }  
```

---

## Comment 4

> Username: AJIOB  
> Created at: 2025-12-21 13:37:02 UTC  
> Url: https://github.com/boostorg/process/issues/529#issuecomment-3678802501  

Looks like we forgot to reset the pipe fd value after closing in child and receive a RAII destruction (second close) for it:  
https://github.com/boostorg/process/blob/2a41d0a0dcef77ff362c1e3a7cc88b47191ff9d2/include/boost/process/v2/posix/default_launcher.hpp#L374

---

## Comment 5

> Username: AJIOB  
> Created at: 2025-12-22 04:49:17 UTC  
> Url: https://github.com/boostorg/process/issues/529#issuecomment-3680425024  

Oops, I forgot the brackets near `printf("4\n");`  
  
Here is fixed patch:  
```cxx  
            else if (pid == 0)  
            {  
                ::close(pg.p[0]);  
#if !defined(BOOST_PROCESS_V2_DISABLE_NOTIFY_FORK)  
                ctx.notify_fork(net::execution_context::fork_child);  
#endif  
                printf("1\n");  
                ec = detail::on_exec_setup(*this, executable, argv, inits...);  
                printf("2: %d\n", ec);  
                if (!ec)  
                {  
                    printf("3\n");  
                    close_all_fds(ec);  
                }  
                if (!ec)  
                {  
                    printf("4\n");  
                    ::execve(executable.c_str(), const_cast<char * const *>(argv), const_cast<char * const *>(env));  
                }  
  
                printf("5: %d\n", ec);  
  
                ignore_unused(::write(pg.p[1], &errno, sizeof(int)));  
                BOOST_PROCESS_V2_ASSIGN_EC(ec, errno, system_category());  
                detail::on_exec_error(*this, executable, argv, ec, inits...);  
                ::exit(EXIT_FAILURE);  
                return basic_process<Executor>{exec};  
            }  
```  
  
And the output:  
```  
./q   
Creating process...  
1  
2: 0  
3  
5: 2  
Exception: default_launcher: No such file or directory [system:2 at /embuild/sysroot/src/cammeta-cpp/build/axxonsoft/sdk/debian10.armhf.gcc8.3.0/cammeta-cpp/boost_1_89_0/include/boost/process/v2/posix/default_launcher.hpp:417 in function 'operator()']. Error code: system:2  
```  
  
So, the error has been returned from the `close_all_fds()` call

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2025-12-22 05:53:24 UTC  
> Url: https://github.com/boostorg/process/issues/529#issuecomment-3680589274  

Can you figure you which `close_all` function gets used?  
  
```cpp  
https://github.com/boostorg/process/blob/2a41d0a0dcef77ff362c1e3a7cc88b47191ff9d2/src/posix/close_handles.cpp  
```  
  
I reckon it's possible it uses this one: https://github.com/boostorg/process/blob/2a41d0a0dcef77ff362c1e3a7cc88b47191ff9d2/src/posix/close_handles.cpp#L181  
  
Which iterates over `/dev/fd` which may fail if the directory structure is set up differently. If so, does your OS have an equivalent of `/dev/fd` ?

---

## Comment 7

> Username: AJIOB  
> Created at: 2025-12-22 06:01:31 UTC  
> Updated at: 2025-12-22 06:03:03 UTC  
> Url: https://github.com/boostorg/process/issues/529#issuecomment-3680602092  

Hi @klemens-morgenstern ,  
  
Yes, you're right, we have no `/dev/fd`. We have a `/proc/self/fd`, and the `/dev/fd` should point on it, as I can see [in kernel docs](https://www.kernel.org/doc/html/latest/admin-guide/devices.html#compulsory-links).  
  
What about trying to open `/proc/self/fd` as a second hope if the `dir` smart pointer is empty?

---

## Comment 8

> Username: AJIOB  
> Created at: 2025-12-22 06:07:42 UTC  
> Url: https://github.com/boostorg/process/issues/529#issuecomment-3680613745  

P.S. As I can see in disassembled test binary, you're right - we are using https://github.com/boostorg/process/blob/2a41d0a0dcef77ff362c1e3a7cc88b47191ff9d2/src/posix/close_handles.cpp#L181

---

## Comment 9

> Username: klemens-morgenstern  
> Created at: 2025-12-22 06:11:11 UTC  
> Url: https://github.com/boostorg/process/issues/529#issuecomment-3680620488  

Sounds right, but then the question is: what do we do if `/proc/self/fd` isn't available? Just not closing handles sounds worse - do you have any ideas?

---

## Comment 10

> Username: AJIOB  
> Created at: 2025-12-22 06:36:46 UTC  
> Url: https://github.com/boostorg/process/issues/529#issuecomment-3680676070  

Possibly we can generate some warning for the user. It's sounds not good, but it's not really critical.  
  
To have the similar behavior, we're force marking all sockets and other descriptors with `O_CLOEXEC` after cleating/opening.

---

## Comment 11

> Username: AJIOB  
> Created at: 2025-12-22 10:37:58 UTC  
> Updated at: 2025-12-22 18:07:03 UTC  
> Url: https://github.com/boostorg/process/issues/529#issuecomment-3681500283  

I've provided the PR #538, that help with our small example.  
  
I hope it will be merged
