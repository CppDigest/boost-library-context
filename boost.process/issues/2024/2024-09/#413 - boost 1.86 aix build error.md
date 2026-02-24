# #413 - boost 1.86 aix build error [Open]

> Username: paladin-dalao  
> Created at: 2024-09-17 09:47:17 UTC  
> Updated at: 2025-10-11 09:37:46 UTC  
> Url: https://github.com/boostorg/process/issues/413  

aix version  7100-05  
```  
/usr/local/include/boost/process/v2/impl/pid.ipp:729:2: error: #error "Platform not supported"  
 #error "Platform not supported"  
  
/usr/local/include/boost/process/v2/ext/impl/exe.ipp:175:2: error: #error "Platform not supported"  
 #error "Platform not supported"  
  ^~~~~  
  
/usr/local/include/boost/process/v2/impl/error.ipp: In member function 'virtual std::__cxx11::string boost::process::v2::error::detail::exit_code_category::message(int) const':  
/usr/local/include/boost/process/v2/impl/error.ipp:112:21: error: duplicate case value  
                     case SIGLOST: return "SIGLOST:   File lock lost (unused)";  
```

---

## Comment 1

> Username: paladin-dalao  
> Created at: 2024-10-24 08:42:10 UTC  
> Url: https://github.com/boostorg/process/issues/413#issuecomment-2435647578  

When will aix be supported?

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2024-10-24 22:03:36 UTC  
> Updated at: 2024-10-24 22:43:56 UTC  
> Url: https://github.com/boostorg/process/issues/413#issuecomment-2436414736  

If I get help implementing & testing it, sure. I don't have access to an AIX machine.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2024-10-24 22:46:53 UTC  
> Url: https://github.com/boostorg/process/issues/413#issuecomment-2436470740  

The implementations needed are:  
  
```cpp  
std::vector<pid_type> all_pids(boost::system::error_code & ec);  
pid_type parent_pid(pid_type pid, boost::system::error_code & ec);  
std::vector<pid_type> child_pids(pid_type pid, boost::system::error_code & ec);  
```  
  
probaly using https://www.ibm.com/docs/en/aix/7.2?topic=g-getprocs-subroutine  
  
and   
  
```cpp  
filesystem::path exe(boost::process::v2::pid_type pid, boost::system::error_code & ec)  
```  
  
which could probably just always return a "operation not supported" error.

---

## Comment 4

> Username: paladin-dalao  
> Created at: 2025-01-13 10:06:24 UTC  
> Url: https://github.com/boostorg/process/issues/413#issuecomment-2586678414  

> The implementations needed are:  
>   
> std::vector<pid_type> all_pids(boost::system::error_code & ec);  
> pid_type parent_pid(pid_type pid, boost::system::error_code & ec);  
> std::vector<pid_type> child_pids(pid_type pid, boost::system::error_code & ec);  
> probaly using https://www.ibm.com/docs/en/aix/7.2?topic=g-getprocs-subroutine  
>   
> and  
>   
> filesystem::path exe(boost::process::v2::pid_type pid, boost::system::error_code & ec)  
> which could probably just always return a "operation not supported" error.  
  
You can provide a code package that supports AIX functions, and I'll conduct the testing and verification.

---

## Comment 5

> Username: paladin-dalao  
> Created at: 2025-01-13 11:32:16 UTC  
> Url: https://github.com/boostorg/process/issues/413#issuecomment-2586860278  

<img width="688" alt="Image" src="https://github.com/user-attachments/assets/042f8b69-dc12-4c46-a55c-581b98112726" />

---

## Comment 6

> Username: paladin-dalao  
> Created at: 2025-02-07 07:48:28 UTC  
> Url: https://github.com/boostorg/process/issues/413#issuecomment-2642164738  

In the Boost code, I don't know how to implement these error codes.

---

## Comment 7

> Username: paladin-dalao  
> Created at: 2025-02-17 06:11:21 UTC  
> Url: https://github.com/boostorg/process/issues/413#issuecomment-2662149765  

I hope it can support the implementation of the AIX system.

---

## Comment 8

> Username: paladin-dalao  
> Created at: 2025-02-20 09:58:03 UTC  
> Url: https://github.com/boostorg/process/issues/413#issuecomment-2671007594  

@klemens-morgenstern help

---

## Comment 9

> Username: paladin-dalao  
> Created at: 2025-02-20 09:59:23 UTC  
> Url: https://github.com/boostorg/process/issues/413#issuecomment-2671010846  

It is possible to provide a beta version. I will conduct debugging and fixing, and then submit a Pull Request

---

## Comment 10

> Username: klemens-morgenstern  
> Created at: 2025-02-20 10:11:22 UTC  
> Url: https://github.com/boostorg/process/issues/413#issuecomment-2671041156  

I can't write the code, i do not have access to an AIX machine.

---

## Comment 11

> Username: paladin-dalao  
> Created at: 2025-02-27 03:55:44 UTC  
> Url: https://github.com/boostorg/process/issues/413#issuecomment-2686793278  

> I can't write the code, i do not have access to an AIX machine.  
  
You can write the core code based on the documentation of AIX, and I will do the debugging and building， OK？

---

## Comment 12

> Username: klemens-morgenstern  
> Created at: 2025-02-28 07:35:59 UTC  
> Url: https://github.com/boostorg/process/issues/413#issuecomment-2689941219  

Writing code blind like this doesn't work, why can't you just write it?

---

## Comment 13

> Username: paladin-dalao  
> Created at: 2025-03-11 11:09:01 UTC  
> Url: https://github.com/boostorg/process/issues/413#issuecomment-2713718315  

> Writing code blind like this doesn't work, why can't you just write it?  
  
I'm not familiar with the code framework and specifications of Boost, and I don't know how to write it.

---

## Comment 14

> Username: paladin-dalao  
> Created at: 2025-07-31 04:03:40 UTC  
> Url: https://github.com/boostorg/process/issues/413#issuecomment-3138491727  

Why can other functions be compiled and run on aix, but only the process module cannot

---

## Comment 15

> Username: klemens-morgenstern  
> Created at: 2025-07-31 06:45:35 UTC  
> Url: https://github.com/boostorg/process/issues/413#issuecomment-3138748291  

It can be compiled since 46b71d5, which is part of 1.87.

---

## Comment 16

> Username: paladin-dalao  
> Created at: 2025-10-11 09:37:45 UTC  
> Url: https://github.com/boostorg/process/issues/413#issuecomment-3393115218  

Although it can be compiled successfully, the compiled program cannot run properly on AIX.  
  
At 2025-07-31 14:45:56, "Klemens Morgenstern" ***@***.***> wrote:  
  
klemens-morgenstern left a comment (boostorg/process#413)  
  
It can be compiled since 46b71d5, which is part of 1.87.  
  
—  
Reply to this email directly, view it on GitHub, or unsubscribe.  
You are receiving this because you modified the open/close state.Message ID: ***@***.***>
