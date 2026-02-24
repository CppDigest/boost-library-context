# #79 Function invocation support [Closed]

> Username: dulikvor  
> Created at: 2019-03-30 15:00:27 UTC  
> Updated at: 2020-01-26 08:16:57 UTC  
> Closed at: 2020-01-26 08:16:57 UTC  
> Url: https://github.com/boostorg/process/pull/79  

Hi, been trying to add support for functions invocation via a remote process.  
the user can:  
supply a functor, all required arguments.  
invocation will only occur via a real fork (not a virtual one).  
  
this is only a first draft, windows is not supported yet, i didn't really tested all the cases in full, only preformed a sanity one, i corrupted a test in the process, this will not be the final product, documentations are still missing and i guess even more stuff.  
  
but before i complete all, i want to hear your opinion on the suggestion and should i complete this feature in full.

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2019-03-30 15:24:57 UTC  
> Url: https://github.com/boostorg/process/pull/79#issuecomment-478255830  

I don't really get it, do you want to execute a function after fork and before execve? If so, that's already possible by using [`posix::on_exec_setup`](https://www.boost.org/doc/libs/1_70_0_beta1/doc/html/boost/process/extend/on_exec_setup.html)

---

## Comment 2

> Username: dulikvor  
> Created_at: 2019-03-30 15:29:19 UTC  
> Url: https://github.com/boostorg/process/pull/79#issuecomment-478256225  

> I don't really get it, do you want to execute a function after fork and before execve? If so, that's already possible by using [`posix::on_exec_setup`](https://www.boost.org/doc/libs/1_70_0_beta1/doc/html/boost/process/extend/on_exec_setup.html)  
  
Neither, the function invocation replaces the execve functionality, you provide your own logic and it will be invoked under the spawned process. see the spawn.cpp test for example, my lambda is the one being invoked after the fork and my printout in retrieved via the pipe.

---

## Comment 3

> Username: klemens-morgenstern  
> Created_at: 2019-03-30 15:42:16 UTC  
> Url: https://github.com/boostorg/process/pull/79#issuecomment-478257403  

That does sound very specific and isn't possible on windows, because win doesn't have fork. You can do what you want by overloading the [on_exec_setup handler](https://github.com/boostorg/process/blob/develop/include/boost/process/detail/posix/handler.hpp#L14)  and you can access the `executor`. That should work as long as you call exit properly.   
  
Everything in the executor is public to, so you can also use `set_error`.  
  
If you need help let me know, but I don't this is generic or portable enough to be part of this library.

---

## Comment 4

> Username: dulikvor  
> Created_at: 2019-03-30 16:36:29 UTC  
> Updated_at: 2019-03-30 17:04:54 UTC  
> Url: https://github.com/boostorg/process/pull/79#issuecomment-478262259  

> That does sound very specific and isn't possible on windows, because win doesn't have fork. You can do what you want by overloading the [on_exec_setup handler](https://github.com/boostorg/process/blob/develop/include/boost/process/detail/posix/handler.hpp#L14) and you can access the `executor`. That should work as long as you call exit properly.  
>   
> Everything in the executor is public to, so you can also use `set_error`.  
>   
> If you need help let me know, but I don't this is generic or portable enough to be part of this library.  
  
I do agree windows is problematic, the process model is quite different, but - the library as it is today is not fully portable due to those differences. for example - the extension you suggested only exists in posix as far as i recall.  
  
i don't think its also really equivalent in term of user interface, providing the user the mean to provide its logic as a functor directly and the relevant arguments is much more convenient than the other form you suggested, not to mention the case where i only want my logic to run under a fork without the usage of execvp, requiring me to call exit on my behalf is quite a hack, i just want to run my logic, not to give any thought why the boost implementation forces me to call exit, or what is an executor, not to mention the fact some of the executor state is publicly accessible and can be easily corrupted by the user.  
   
i think the real question is, how portable do we really need to be, is it mandatory to have every little feature supported by all platform, or in case - one platform provides an opportunity we can add a bit more to it.  
  
Edit:  
the entire feature can be placed under the posix section, such as vfork, the extension you mentioned and other non portable features, it will provide more flexibility to unix users, a special capability already existing solely in the unix platform, only with a better interface.  
  
 it can also support function pointers to make it even more generic.   
  
i think its worth it.

---

## Comment 5

> Username: klemens-morgenstern  
> Created_at: 2019-03-30 17:14:32 UTC  
> Url: https://github.com/boostorg/process/pull/79#issuecomment-478266607  

Why doesn't my suggested solution work?

---

## Comment 6

> Username: dulikvor  
> Created_at: 2019-03-30 19:16:08 UTC  
> Updated_at: 2019-03-30 19:18:20 UTC  
> Url: https://github.com/boostorg/process/pull/79#issuecomment-478280019  

> Why doesn't my suggested solution work?  
  
Before we get into that, i just want to make sure i don't waste your time:  
from design point of view - is it sounds reasonable to provide the functionality of running user provided functions at this library, or it only meant to be kept at the lowest level of spawning processes and running command line like operations?  
  
if the answer is no for the first, than this feature is non relevant and i'm sorry for wasting your time (and mine in writing it :-) ).

---

## Comment 7

> Username: klemens-morgenstern  
> Created_at: 2019-03-31 05:41:30 UTC  
> Url: https://github.com/boostorg/process/pull/79#issuecomment-478313943  

It is meant to give a portable way to spawn processes plus give the user access for his own extensions.  
  
As far as I can tell: what you want to do is not portable but already possible through extensions, so I don't see why it should be part of the library.

---

## Comment 8

> Username: dulikvor  
> Created_at: 2019-03-31 11:03:25 UTC  
> Url: https://github.com/boostorg/process/pull/79#issuecomment-478331695  

> It is meant to give a portable way to spawn processes plus give the user access for his own extensions.  
>   
> As far as I can tell: what you want to do is not portable but already possible through extensions, so I don't see why it should be part of the library.  
  
Well, not really, yes you can run user provided functions using the extensions you mentioned, but, they are not meant for the use case i'm pointing at -  
  
lets say our user wishes to spawn a process and to run his own logic provided as a function.  
if you want to use the existing extension to do that, he will have:  
1) Create a wrapper, since the calling handler dictates a specific prototype (you need to receive the executor), so if i the user wishes to provide some other arguments in runtime, he will have to to capture them first:  
```c++  
template<typename... Args>  
struct functor_handler  
{  
    template<typename Callable, typename... _Args>  
    functor_handler(Callable func, _Args&&... args)  
        :m_func(func), m_args(std::forward<_Args>(args)...)  
    {  
    }  
      
    functor_handler(const functor_handler&) = default;  
      
    template<typename Executor>  
    void operator()(Executor& exec) const  
    {  
        invoke(boost::mp11::make_index_sequence<sizeof...(Args)>());  
        _exit(0);  
    }  
  
private:  
    template<std::size_t... I>  
    void invoke(boost::mp11::index_sequence<I...>) const  
    {  
        m_func(std::get<I>(m_args)...);  
    }  
  
  
private:  
    std::function<void(Args...)> m_func;  
    std::tuple<Args...> m_args;  
};  
```  
he will then have to use the on_exec_setup maker object in order to register it:  
```c++  
bp::spawn(  
        bp::extend::on_exec_setup =functor_handler<int>(  
            [](const int& val){std::cout <<"seems_to_be_working_"<<val<<std::endl; }, 5),  
        bp::std_out > is,  
        ec  
    );  
```  
in my example above the out_pipe handler is potentially being invoked only after my personal handler so i can't use the pipe in order to propagate my printout to the parent process.  
  
i'm calling exit(0) directly since other wise execvp will fail.  
  
if the user is using c++11 (and boost.process requires c++11 at minimum) he will not be able to provide lambda directly since the Executor is templated with Sequence.  
```c++[...](auto&){...} ``` generic lambdas are only available from c++14.  
  
the executor state is not safe, i can access the executor state and corrupt it.  
i need to have knowledge regarding the executor, even tho i don't care about it, or wishes to use it.  
i just wanted to invoke my own logic.  
  
and the equivalent to all that using my feature:  
```c++  
bp::spawn(  
        [](const int& val){std::cout <<"seems_to_be_working_"<<val<<std::endl; },  
        bp::f_args(5),  
        bp::std_out > is,  
        ec  
    );  
```  
2) That capability by it self is not portable even today, on_exec_setup is only available as an extension in linux.  
  
The real question is, in my opinion - do you think there is a place at this library for linux users (**yes, it will not be portable** up until microsoft will give us the fork we so desperately need :)), to spawn their own functions as the main logic of the child process, instead of using the execvp command.

---

## Comment 9

> Username: klemens-morgenstern  
> Created_at: 2019-03-31 12:12:31 UTC  
> Url: https://github.com/boostorg/process/pull/79#issuecomment-478336247  

Why wouldn't he use C++14 or C++17 instead? And it is unlikely windows will ever give us a `fork`. If you want it portably, you can just use a real process. You can have an app calling itself.  
  
What exacty is your use-case, that you can't write a five-line wrapper? I mean you can just write  that and even use the return code- plus add `std::forward` :  
  
```cpp  
  
const my_func = [](const int& val){std::cout <<"seems_to_be_working_"<<val<<std::endl; return val;};  
  
const wrap = [](Func && func, auto && args){  
                  return bp::extend::on_exec_setup([](auto & exec){  
                       const auto res = func(...args);  
                       static std::string exit_str = "";  
                       exit_str = std::to_string(res); //so it survives scope exit  
                       exec.exe = "/bin/sh";   
                       exec.cmd_line = {"-c", "exit", exit_str.c_str(), nullptr};  
                   };  
  
bp::spawn(wrap(my_func, 42), ec);  
```  
  
I still don't see why it needs to be a feature in this library if you can just write an extension like that.

---

## Comment 10

> Username: klemens-morgenstern  
> Created_at: 2019-03-31 12:13:24 UTC  
> Url: https://github.com/boostorg/process/pull/79#issuecomment-478336301  

Also what keeps you from just using `fork`? What other features do you need?

---

## Comment 11

> Username: dulikvor  
> Created_at: 2019-03-31 12:34:42 UTC  
> Url: https://github.com/boostorg/process/pull/79#issuecomment-478337881  

> Also what keeps you from just using `fork`? What other features do you need?  
  
In your example if the user wishes to use other handlers it will not work, the user will have to take into account the order of the handlers he provided. i gave a small example above with out_pipe.  
  
the fact you need to provide a specific wrapper (it doesn't matter if it is short or long), take into account the order of the different handlers, providing a mean to "survive execvp" means we are a bit abusing the original intent of that extension. it was not meant to replace execvp, it was meant to be invoked as a hook prior to it.  
  
btw, the user may also engage that handler with virtual fork, which is problematic.  
  
why not just use fork? i want all the other features already existing with in the library - handling IO, error handling, and much more. i'm not looking to rival boost.process only to add something small i think its still missing.

---

## Comment 12

> Username: klemens-morgenstern  
> Created_at: 2019-03-31 15:17:32 UTC  
> Url: https://github.com/boostorg/process/pull/79#issuecomment-478350625  

Then write a wrapper function and put the function handler at the end.   
  
I don't think it belongs in the library, it is to niche and introduces too many corner cases for me. You still haven't told me your use-case, so it like you just think it's a useful feature but unless I know the problem you want to solve, I have no way of judging how useful it actually is.

---

## Comment 13

> Username: dulikvor  
> Created_at: 2019-03-31 15:30:23 UTC  
> Updated_at: 2019-04-01 05:50:49 UTC  
> Url: https://github.com/boostorg/process/pull/79#issuecomment-478351647  

> Then write a wrapper function and put the function handler at the end.  
>   
> I don't think it belongs in the library, it is to niche and introduces too many corner cases for me. You still haven't told me your use-case, so it like you just think it's a useful feature but unless I know the problem you want to solve, I have no way of judging how useful it actually is.  
  
ah, sure, in my case it was an attempt to write a non portable executor which is based on a pool of processes for the boost interprocess library. at some point i needed to spawn all processes of that pool and their executor code, i came to this library since it was the natural place for my requirements, but it lacked the feature i needed.  
  
i could have solved my problem using fork, but i think it should be done correctly, also using the method you suggested is not right in my opinion due to the reasons i depicted above, requiring me to manipulate the the on_exec_setup handler is not a proper solution, when you designed that handler and its usage, it was not done for the issue i'm facing.  
  
here is a poc i wrote just for my self as a play ground, you can see the use case in more detail:  
https://github.com/Dudi119/Core/blob/master/src/AsyncExecutor.h

---

## Comment 14

> Username: dulikvor  
> Created_at: 2019-04-02 07:31:41 UTC  
> Url: https://github.com/boostorg/process/pull/79#issuecomment-478879088  

@klemens-morgenstern any thoughts?

---

## Comment 15

> Username: klemens-morgenstern  
> Created_at: 2019-04-02 07:47:38 UTC  
> Url: https://github.com/boostorg/process/pull/79#issuecomment-478883958  

I still don't see this as a part of `boost.process´, though I think you're somewhat right about my solution of using the `on_exec_setup` being a bit hacky as well. IMO the clean solution is a recursive call with an argument telling the program to execute your function.  
  
I think the best choice would be to manually use `fork` - you can still use pipes and all the other fun stuff then.

---
