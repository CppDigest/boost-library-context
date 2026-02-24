# #8 - Performance degrades [Closed]

> Username: thearttrooper  
> Created at: 2016-12-14 12:52:01 UTC  
> Updated at: 2016-12-16 06:15:19 UTC  
> Closed at: 2016-12-16 06:15:19 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/8  

First - let me thank you for the library.  
  
Following the documentation I created a simple Win32 console program. The program contains a recursive function to compute the Fibonnaci number. I included a call to dump the stack at the start of the function.  
  
The function looks like:  
  
```  
int fib(n)  
{  
  cout << boost::stacktrace::stacktrace();  
  
  if (0 == n)  
    return 0;  
  if (1 == n)  
    return 1;  
  
  return fib(n - 1) + fib(n - 2);  
}  
```  
  
I call the function with:  
  
```  
  for (int iii = 0; iii < 13; ++iii)  
    cout << iii << ": " << fib(iii) << '\n';  
```  
  
Update: in testing I added a condition to only write out the stack after 10 levels of recursion. To better seen the effect of memory growth and progressive slow-down I've updated the code to remove the condition.  
  
After a few calls, the output of the stack track to the console slows to a crawl and the process memory grows to ~700MB.  
  
I compiled the program using Visual Studio 2015 Update 5. The rest of the boost libraries came from version 1.62.

---

## Comment 1

> Username: apolukhin  
> Created at: 2016-12-15 19:45:36 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/8#issuecomment-267424666  

I can definitely improve that on 1/3. But that would be a crawling on steroids... I'll try to improve the `try_init_com` function https://github.com/apolukhin/stacktrace/blob/master/include/boost/stacktrace/detail/backend_windows.hpp#L60  
  
This may give big speedups, but may not.

---

## Comment 2

> Username: apolukhin  
> Created at: 2016-12-15 20:13:15 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/8#issuecomment-267431566  

OK, I've got an idea on improvement. In best case it will give ~30 times speedup on Windows. In worst case - nothing.  
  
But I'll do that after the Boost review (on successful review, I'll do that before merging the library into Boost)
