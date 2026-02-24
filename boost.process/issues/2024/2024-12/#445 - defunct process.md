# #445 - defunct process [Closed]

> Username: jean-christophe81  
> Created at: 2024-12-30 14:22:49 UTC  
> Updated at: 2025-01-13 01:31:54 UTC  
> Closed at: 2025-01-13 01:31:53 UTC  
> Url: https://github.com/boostorg/process/issues/445  

Hello  
  
I'm using your library in both linux and windows versions. Thanks for your work.  
  
I had to disable notify_fork and I see you have added a flag to this. I have another issue under linux. If you try to exec an non existing executable, child process still exist in defunct state. I had to to do this fix in posix launcher:  
  
```cpp  
 template <typename Executor, typename Args, typename... Inits>  
  auto operator()(  
........  
.......  
      if (ec) {  
        **if (pid > 0) {  
          ::kill(pid, SIGKILL);  
          ::waitpid(pid, nullptr, 0);  
        }**  
        detail::on_error(*this, executable, argv, ec, inits...);  
        return basic_process<Executor>{exec};  
      }  
    }  
    basic_process<Executor> proc(exec, pid);  
    detail::on_success(*this, executable, argv, ec, inits...);  
    return proc;  
  }  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-12-31 16:36:32 UTC  
> Updated at: 2024-12-31 16:36:40 UTC  
> Url: https://github.com/boostorg/process/issues/445#issuecomment-2566579959  

So, zombie process on execve failure?

---

## Comment 2

> Username: jean-christophe81  
> Created at: 2025-01-02 09:35:06 UTC  
> Url: https://github.com/boostorg/process/issues/445#issuecomment-2567492450  

yes, I had a zombie child process.
