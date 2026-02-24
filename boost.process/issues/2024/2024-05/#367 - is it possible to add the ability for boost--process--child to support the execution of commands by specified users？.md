# #367 - is it possible to add the ability for boost::process::child to support the execution of commands by specified users？ [Open]

> Username: zhixingchen0629  
> Created at: 2024-05-08 02:23:53 UTC  
> Updated at: 2024-05-08 07:24:58 UTC  
> Url: https://github.com/boostorg/process/issues/367  

It is very common to specify a user to start a process to execute related commands. In the boost::process library, it is not supported to create a specified user process to execute tasks. On the linux side, the executor of the child process can be modified through a custom on_exec_setup function, but on the windows side, there is no such hook point as linux to specify the user-created process. In boost::process, the windows side creates the process by calling CreateProcess. When i need to specify a user to create a process,  i need to call CreateProcessAsUser to specify the user to create the process and specify the task. Is it possible to add the function to support the specified user to create the process and execute commands?

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-05-08 04:07:46 UTC  
> Url: https://github.com/boostorg/process/issues/367#issuecomment-2099700111  

This is supported in v2 by using the `as_user_launcher`.

---

## Comment 2

> Username: zhixingchen0629  
> Created at: 2024-05-08 06:43:01 UTC  
> Url: https://github.com/boostorg/process/issues/367#issuecomment-2099853734  

Is this feature integrated into the official release version? If so, in which version was it incorporated?

---

## Comment 3

> Username: zhixingchen0629  
> Created at: 2024-05-08 07:24:57 UTC  
> Url: https://github.com/boostorg/process/issues/367#issuecomment-2099920509  

I find this part was merged in version `1.80.0`
