# #101 - check file exist is invalid ? [Closed]

> Username: 0x007004  
> Created at: 2020-02-17 10:12:05 UTC  
> Updated at: 2020-02-17 14:01:12 UTC  
> Closed at: 2020-02-17 10:24:06 UTC  
> Url: https://github.com/boostorg/log/issues/101  

https://github.com/boostorg/log/blob/42b4463ad26b19a8e2fc767101614ad5836037c4/src/text_file_backend.cpp#L1501  
  
`   if (!!m_pImpl->m_pFileCollector)`  
`    {`  
         `// Check if the file has not been deleted by another process`  
        `system::error_code ec;`  
        `filesystem::file_status status = filesystem::status(prev_file_name, ec);`  
       ` if (status.type() == filesystem::regular_file)`  
            `m_pImpl->m_pFileCollector->store_file(prev_file_name);`  
   ` }`  
the code will check the file is exist, if the file don't exist the 'store_file'   function will be throw expection ,because `last_write_time` and `file_size` .  
 the condition `if (status.type() == filesystem::regular_file) `   can`t protect it.  
1. check file exist   
2. if exist call store_file  , but before call store_file the file will be delete .  
  
**Question:**  
I have to catch the exaction ?  
And another question `if (!!m_pImpl->m_pFileCollector)`  why use "!!" not be `if(m_pImpl->m_pFileCollector)` ?  
  
Thankyou

---

## Comment 1

> Username: Lastique  
> Created at: 2020-02-17 10:24:06 UTC  
> Url: https://github.com/boostorg/log/issues/101#issuecomment-586921802  

Yes, `store_file` can still throw. It can also throw for reasons not related to the presence of the file being rotated, so yes, you should catch the exception. You could argue that the check is redundant, but not invalid. I suppose, I could remove it and simply always throw.  
  
The `!!x` sequence is equivalent to `x`, but may be slightly more efficient if *unspecified bool* idiom is used. The type conversion operator may be returning a member pointer type, which is larger than `sizeof(void*)` and require more than one register to return a value. The compiler may optimize it, but the double negation trick circumvents that potential inefficiency completely.

---

## Comment 2

> Username: 0x007004  
> Created at: 2020-02-17 11:45:08 UTC  
> Updated at: 2020-02-17 11:46:05 UTC  
> Url: https://github.com/boostorg/log/issues/101#issuecomment-586955921  

Thankyou , I will catch the exception , I known I can use `set_exception_handler`  this handler slove the problem , but the handler is a no parameters callback function ,  so the exception will be ignore.  when my app does not have log . I don`t know what wrong with my app .   
  
What can I do about this . I want know what wrong.

---

## Comment 3

> Username: Lastique  
> Created at: 2020-02-17 11:51:43 UTC  
> Url: https://github.com/boostorg/log/issues/101#issuecomment-586959260  

I'm not sure I understand. The exception handler is called in a `catch` statement, so you can simply use `throw` to re-throw the exception and catch it yourself. There are [tools](https://www.boost.org/doc/libs/1_72_0/libs/log/doc/html/log/detailed/utilities.html#log.detailed.utilities.exception_handlers) that automate this.  
  
However, exception handlers will not help when you invoke `rotate_file` yourself. You will need to catch exceptions in your code when you call that method directly.

---

## Comment 4

> Username: 0x007004  
> Created at: 2020-02-17 12:42:53 UTC  
> Url: https://github.com/boostorg/log/issues/101#issuecomment-586976353  

Thankyou, I will try this .

---

## Comment 5

> Username: Lastique  
> Created at: 2020-02-17 12:43:05 UTC  
> Url: https://github.com/boostorg/log/issues/101#issuecomment-586976413  

Sorry, I don't understand the problem.

---

## Comment 6

> Username: 0x007004  
> Created at: 2020-02-17 13:18:51 UTC  
> Url: https://github.com/boostorg/log/issues/101#issuecomment-586989764  

sorry , I'm not good at handling c++ exception modules. Your advice helped me， it is so cool. why the functor have the param `void operator() (std::runtime_error const& e) const`  and then when catch the exception handler does not have param .   
        `catch (...)`  
        `{`  
           ` boost::log::aux::shared_lock_guard< mutex_type > frontend_lock(this->frontend_mutex());`  
           ` if (this->exception_handler().empty())`  
                `throw;`  
            `this->exception_handler()();`  
            `return false;`  
        `}`  
![image](https://user-images.githubusercontent.com/13299372/74656917-1d988580-51ca-11ea-9048-68e4734efe68.png)

---

## Comment 7

> Username: Lastique  
> Created at: 2020-02-17 13:41:57 UTC  
> Url: https://github.com/boostorg/log/issues/101#issuecomment-586999384  

> why the functor have the param `void operator() (std::runtime_error const& e) const` and then when catch the exception handler does not have param .  
  
As I said earlier, the exception handler is called from a `catch` block, which means it should re-throw the exception to obtain it. This is what the handler generated by `make_exception_handler` does, see [here](https://github.com/boostorg/log/blob/34d9e64815894ef549d30b84d29aa34dce03c58f/include/boost/log/utility/exception_handler.hpp#L73).

---

## Comment 8

> Username: 0x007004  
> Created at: 2020-02-17 14:01:12 UTC  
> Url: https://github.com/boostorg/log/issues/101#issuecomment-587007464  

thank you ,I understand about this.
