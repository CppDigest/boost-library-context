# #10 Variadic wait functions [Merged]

> Username: kylelutz  
> Created at: 2014-05-21 06:01:48 UTC  
> Updated at: 2014-07-12 15:45:46 UTC  
> Merged at: 2014-05-21 17:48:04 UTC  
> Closed at: 2014-05-21 17:48:04 UTC  
> Url: https://github.com/boostorg/thread/pull/10  



---

## Comment 1

> Username: viboes  
> Created_at: 2014-05-21 06:22:01 UTC  
> Url: https://github.com/boostorg/thread/pull/10#issuecomment-43716119  

This pull-request needs to be complemented with some documentation and tests to maybe taken in account.   
  
Why do you need this new feature? How can you do it without? ....

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-05-21 14:33:18 UTC  
> Url: https://github.com/boostorg/thread/pull/10#issuecomment-43761884  

This is already tested by the same test functions which check the 2, 3, 4, and 5 argument versions of `wait_for_any()` and `wait_for_all()`. The tests will just use the variadic versions of the functions if they are supported by the compiler. I guess I could add the same tests for `6`, `7`, `8`, and `9` futures though I don't know how much additional coverage they would provide.  
  
I took a look at the documentation but I was unsure how to best indicate that these functions are variadic only if the compiler supports variadic templates. What would you recommend?  
  
This feature is useful for users wishing to wait on more than `5` different future objects (but perhaps `5` has some significance I'm unware of). I have similar functions locally but I though these would be more generally useful.

---

## Comment 3

> Username: viboes  
> Created_at: 2014-05-21 17:47:53 UTC  
> Url: https://github.com/boostorg/thread/pull/10#issuecomment-43790953  

I'm really sorry. I misunderstood your request. I believed that this was an extension for an additional feature, but I see now that it is just an implementation detail for compilers supporting variadic templates.   
  
I would merge it soon.

---

## Comment 4

> Username: viboes  
> Created_at: 2014-05-21 17:58:22 UTC  
> Url: https://github.com/boostorg/thread/pull/10#issuecomment-43792278  

Hrr, your pull request doesn't compiles :(

---

## Comment 5

> Username: viboes  
> Created_at: 2014-05-21 18:04:40 UTC  
> Updated_at: 2014-05-21 18:06:03 UTC  
> Url: https://github.com/boostorg/thread/pull/10#issuecomment-43793016  

It seems   
  
there is just an error in the first conditional, that must be #ifndef  
  
```  
+#ifdef BOOST_NO_CXX11_VARIADIC_TEMPLATES  
+            template<typename F1, typename... Fs>  
+            void add(F1& f1, Fs&... fs)  
+            {  
+              add(f1); add(fs...);  
+            }  
+#endif  
```

---
