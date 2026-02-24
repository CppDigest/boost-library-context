# #5 - reenter? [Closed]

> Username: Memoryss  
> Created at: 2016-10-27 15:38:59 UTC  
> Updated at: 2016-12-12 08:27:18 UTC  
> Closed at: 2016-12-11 14:10:29 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/5  

when i create push_type pointer. after doing something, I need to stop this coroutine , i delete this push_type pointer . after this, why this coroutine reenter funtion and resume?

---

## Comment 1

> Username: olk  
> Created at: 2016-12-11 14:10:29 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/5#issuecomment-266284305  

Sorry, I don't really understand your point.  
First you should never call 'delete this' - especially when 'this' was created by the framework (boost.coroutine2). Second, if the coroutine was not finished, but its instance goes out of scope it renters the coroutine and cleans-up the stack (destructing all objects, deallocating the stack).

---

## Comment 2

> Username: Memoryss  
> Created at: 2016-12-12 07:35:35 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/5#issuecomment-266361269  

If I can't call 'delete this', how do I interrupt coroutine2?

---

## Comment 3

> Username: olk  
> Created at: 2016-12-12 07:39:03 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/5#issuecomment-266361772  

What do you mean with interrupting a coroutine? A coroutine can be resumed or suspended - interruption makes more sense in the context of threads/tasks.   
delete this men's destroying the object.

---

## Comment 4

> Username: Memoryss  
> Created at: 2016-12-12 07:51:26 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/5#issuecomment-266363526  

In the previous 1.60 Coroutine, I use Coroutine::call_type::swap() to restart this Coroutine. But now, I don't know how to restart this Coroutine2.

---

## Comment 5

> Username: olk  
> Created at: 2016-12-12 08:03:48 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/5#issuecomment-266365472  

I you do swap() on a temporary instance the coroutine gets not resumed, its entered to be destructed (stack unwound ...).  
coroutines are resumed by calling operator()().

---

## Comment 6

> Username: Memoryss  
> Created at: 2016-12-12 08:14:47 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/5#issuecomment-266367196  

`void CoroutineJob::start()  
 {  
    Coroutine::call_type tmp(m_f);  
    m_rt.swap(tmp);  
    resume();  
}  
  
void CoroutineJob::restart()   
{  
    unwind();  
    start();  
}  
  
void CoroutineJob::resume()   
{  
    if (m_rt)  
    {  
        m_rt(None());  
    }  
}  
  
void CoroutineJob::unwind()   
{  
    Coroutine::call_type tmp;  
    m_rt.swap(tmp);  
}  
`  
Above is the Coroutine code, I want to migrate to Coroutine2. But I don't know how to write the ‘unwind() ’

---

## Comment 7

> Username: olk  
> Created at: 2016-12-12 08:24:29 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/5#issuecomment-266368825  

You use symmetric coroutines - but symmetric coroutines are not supported by boost.coroutine2. That's because the API was misleading and conceptually incorrect. You should use execution_context from boost.context (provides symmetric context switching) or boost.fiber instead.

---

## Comment 8

> Username: Memoryss  
> Created at: 2016-12-12 08:27:17 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/5#issuecomment-266369326  

All right. Thank you for your answers.
