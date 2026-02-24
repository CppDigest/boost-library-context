# #210 - dead loop when all worker fibers done [Closed]

> Username: rockeet  
> Created at: 2019-08-22 13:52:26 UTC  
> Updated at: 2019-08-23 05:58:17 UTC  
> Closed at: 2019-08-23 05:58:16 UTC  
> Url: https://github.com/boostorg/fiber/issues/210  

I created N-1 worker fiber and use main fiber as the last worker fiber, when things done, it hang in dispatch sometime(at low frequency).  
  
Then I create N worker fibers, and yield in main fiber to wait all workers, the hang disappeared.

---

## Comment 1

> Username: olk  
> Created at: 2019-08-22 14:07:19 UTC  
> Url: https://github.com/boostorg/fiber/issues/210#issuecomment-523922026  

without code I can't say much ....

---

## Comment 2

> Username: rockeet  
> Created at: 2019-08-23 03:52:53 UTC  
> Url: https://github.com/boostorg/fiber/issues/210#issuecomment-524162540  

The code is very long..., and I failed to write a minimized reproduce test case.  
  
It should caused by a thread(say thread A) is finished earlier than its fibers, and another thread B call thread A's fiber.join(). I changed my code and it's OK now.

---

## Comment 3

> Username: olk  
> Created at: 2019-08-23 05:58:16 UTC  
> Url: https://github.com/boostorg/fiber/issues/210#issuecomment-524183373  

fine
