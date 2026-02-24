# #67 - Initialization order fiasco [Closed]

> Username: hia3  
> Created at: 2015-09-16 03:44:52 UTC  
> Updated at: 2015-09-16 11:06:24 UTC  
> Closed at: 2015-09-16 11:06:24 UTC  
> Url: https://github.com/boostorg/fiber/issues/67  

Hello!  
  
It looks like thread local context::active_ depends on another thread local - activation_record::current_rec.  
  
![image](https://cloud.githubusercontent.com/assets/4704208/9895863/21ce6cdc-5c3d-11e5-8384-41397e1f5826.png)  
  
Then it crashes  
  
![image](https://cloud.githubusercontent.com/assets/4704208/9895939/3cb0a514-5c3e-11e5-9897-7be972596a76.png)

---

## Comment 1

> Username: olk  
> Created at: 2015-09-16 06:03:22 UTC  
> Url: https://github.com/boostorg/fiber/issues/67#issuecomment-140637472  

on first access current_rec is intiialized with :  
  
static detail::activation_record \* main_rec() {  
    thread_local static detail::activation_record rec;  
    return & rec;  
}  
  
thread_local  
detail::activation_record::ptr_t  
detail::activation_record::current_rec = main_rec();  
  
and active_ on first access:  
  
static context \* main_context() {  
    static thread_local context mc;  
    static thread_local scheduler sched( & mc);  
    mc.set_scheduler( & sched);  
    return & mc;  
}  
  
thread_local context *  
context::active_ = main_context();

---

## Comment 2

> Username: hia3  
> Created at: 2015-09-16 11:06:17 UTC  
> Url: https://github.com/boostorg/fiber/issues/67#issuecomment-140709378  

Okay. But then https://github.com/olk/boost-fiber/blob/master/doc/overview.qbk#L108 should be changed to 1.60.
