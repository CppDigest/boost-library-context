# #111 - memory leak [Closed]

> Username: ycyclop  
> Created at: 2021-06-29 12:25:32 UTC  
> Updated at: 2024-02-29 17:22:34 UTC  
> Closed at: 2024-02-29 17:22:34 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/111  

When I do a simple stacktrace there is a memory leak that I do not expect:  
```  
int main()  
{  
    int counter = 0;  
    while (counter < 1000)  
    {  
        std::cout << boost::stacktrace::stacktrace();  
        counter++;  
    }  
}  
```  
in debug the application starts with a footprint of ~40k, at the end it raises to ~440k and as you can see all it does is call the stacktrace().  
In my full application I fear it has some other impact on the memory like corruption that causes the application to misbehave after ~1000 calls but I didn't manage to reproduce it in a smaller scale. The issue I encountered is eliminated when I remove this call `std::cout << boost::stacktrace::stacktrace();` in my real application.  
  
This is on a MSVC 2019 environment. toolset v142

---

## Comment 1

> Username: correa  
> Created at: 2021-10-25 13:09:00 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/111#issuecomment-950911482  

We have also found this issue on MSVC 2019 with Boost 1.72.0

---

## Comment 2

> Username: correa  
> Created at: 2021-10-25 14:46:16 UTC  
> Updated at: 2021-10-25 15:32:12 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/111#issuecomment-951003611  

Tested with Boost 1.77.0 and the leak still happens. If **BOOST_STACKTRACE_USE_WINDBG_CACHED** is defined then process memory usage remains stable (and the code runs much faster).

---

## Comment 3

> Username: apolukhin  
> Created at: 2024-02-24 18:33:53 UTC  
> Updated at: 2024-02-24 18:34:28 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/111#issuecomment-1962492969  

Fixed in 27093f24cb2ff187b179ffc561bdf0c27d7cf551 and https://github.com/boostorg/stacktrace/commit/f783534b0f22379e39597cf380f01e7199c24c29  
  
Will update the docs soon
