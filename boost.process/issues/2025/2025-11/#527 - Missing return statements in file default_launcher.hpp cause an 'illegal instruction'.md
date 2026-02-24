# #527 - Missing return statements in file default_launcher.hpp cause an 'illegal instruction' [Open]

> Username: bertwim  
> Created at: 2025-11-18 14:03:30 UTC  
> Updated at: 2025-11-20 16:34:26 UTC  
> Url: https://github.com/boostorg/process/issues/527  

In `boost/process/v2/posix/default_launcher.hpp` there is a template function `invoke_on_success` defined as  
```  
template<typename Launcher, typename Init>  
inline auto invoke_on_success(Launcher & launcher, const filesystem::path &executable,  
                              const char * const * (&cmd_line),  
                              Init && init, derived && )  
-> decltype(init.on_success(launcher, executable, cmd_line))  
{  
      init.on_success(launcher, executable, cmd_line);  
}  
```  
This function is supposed to have a return value, but a `return` statement is missing. This causes (on my installation, using  recent versions of both g++ and clang++) an 'illegal instruction' followed by an inevitable crash.  
  
Adding a return statement solved the problem:  
```  
template<typename Launcher, typename Init>  
inline auto invoke_on_success(Launcher & launcher, const filesystem::path &executable,  
                              const char * const * (&cmd_line),  
                              Init && init, derived && )  
-> decltype(init.on_success(launcher, executable, cmd_line))  
{  
      return init.on_success(launcher, executable, cmd_line);  
}  
```  
The same problem occurs (in the same file `default_launcher.hpp`)  with the routines   
`invoke_on_error`, `invoke_on_fork_error` and `invoke_on_exec_error`

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-11-18 14:04:58 UTC  
> Url: https://github.com/boostorg/process/issues/527#issuecomment-3547787443  

`on_success` is supposed to return `void` - are you using a custom initializer?

---

## Comment 2

> Username: bertwim  
> Created at: 2025-11-18 14:11:38 UTC  
> Updated at: 2025-11-18 15:31:28 UTC  
> Url: https://github.com/boostorg/process/issues/527#issuecomment-3547811351  

Yes, that is what I do.     
In fact, I was familiarizing myself with the boost.process api, using the code I found in **How to use v2 initializers #466** by mark-5345. First I thought that I was still doing something wrong, and I reacted to that post.   
  
But now it seems that I've struck a bug in the code. Either the functions mentioned should be declared `void` and return nothing, or they do return something, but then  there needs to be a `return`.  
  
In my original post, I made a small mistake indicating the culprint header file: it should be  
`boost/process/v2/posix/default_launcher.hpp`. I had left out the `posix` part. I edited the original posting accordingly.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2025-11-19 16:54:47 UTC  
> Url: https://github.com/boostorg/process/issues/527#issuecomment-3553740101  

Well, I am declaring a return type for SFINAE purposes, but you're right that SIGILL shouldn't happen - if anything this should be caught at compile time.

---

## Comment 4

> Username: bertwim  
> Created at: 2025-11-20 16:34:26 UTC  
> Url: https://github.com/boostorg/process/issues/527#issuecomment-3558994753  

Yes, it should be caught at compile time. But primarily the code should be correct, of course.   
From your responses, I take it that `void` is the correct return type, although with adding a `return` statement as indicated before seems to work ok.  
May I assume that the correct fix will be applied for a next release?
