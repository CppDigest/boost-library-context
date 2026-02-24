# #210 - Empty generator [Open]

> Username: rafalg  
> Created at: 2024-10-26 20:04:27 UTC  
> Updated at: 2024-11-13 12:11:51 UTC  
> Url: https://github.com/boostorg/cobalt/issues/210  

Hi,  
  
Thanks for creating cobalt. Feels like python asyncio but in c++.   
Been trying to use generators to model results of paginated api like aws s3::list_objects.  
All in all seems pretty strightforward , I can co_yeild individual response elements and co_return the last one.  
But came across a corner case where api will respond with 0 elements. My outer loop is:  
  
```c++  
auto keys = s3.list_objects("test-bucket");  
  
while (keys) {  
  auto key = co_await keys;  
}  
  
```   
  
In python when list_objects goes out of scope simply terminates generator.  
In cobalt I got the   
  
```c++  
  what():  cobalt::generator returned void  
````  
  
Any help appreciated!

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-10-27 06:22:18 UTC  
> Url: https://github.com/boostorg/cobalt/issues/210#issuecomment-2439872282  

You're missing a `co_return` at the end. That's technically UB, but it works akin to how python would throw a `StopIteration`.   
You can either handle that exception or `co_return` a null value.

---

## Comment 2

> Username: rafalg  
> Created at: 2024-10-27 11:27:30 UTC  
> Url: https://github.com/boostorg/cobalt/issues/210#issuecomment-2439975450  

Totally understood. What I meant is that it forces the generator client code to handle special value like null/empty optional as an additional condition to break the iteration. Would you consider adding such a feature to the cobalt ifself? Following python example maybe something like cobalt_iteration_break that could be thrown from generator impl. Cobalt could catch it and nicely terminate the for loop on the client side.   
  
Im working a lot with paginated apis. Im wrapping them in generators so that the client code can work with continous the stream of data without knowing how many calls have been done to the server to acually fetch them. Adding these special values to handle cases like empty api responses etc is just a bit annoying.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2024-11-13 02:37:09 UTC  
> Url: https://github.com/boostorg/cobalt/issues/210#issuecomment-2472242160  

What's the annoying part? An extra branch for `co_return` in the generator?

---

## Comment 4

> Username: rafalg  
> Created at: 2024-11-13 07:40:21 UTC  
> Url: https://github.com/boostorg/cobalt/issues/210#issuecomment-2472707001  

The annoying part is extra condition I have to put in the client code.  
So instead of  
  
```c++  
while (g) {  
  auto x = co_await g;  
 ...  
}  
````  
  
I have to  
  
```c++  
while (g) {  
  auto x_wrapped_in_optional = co_await g;  
  if (!x) {  
    break;  
  }  
  auto x = *x;  
 ...  
}  
````  
  
Easy to forget, plus and additional case to cover in tests. Also everything returned from generator has to be wrapped into std::optional or something similar to allow this check to happen.

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2024-11-13 10:58:31 UTC  
> Url: https://github.com/boostorg/cobalt/issues/210#issuecomment-2473198301  

Ok, but can't you just throw from the generator then? Because by the time you call `co_await` the generator doesn't know if it's completed or not.

---

## Comment 6

> Username: rafalg  
> Created at: 2024-11-13 12:11:49 UTC  
> Url: https://github.com/boostorg/cobalt/issues/210#issuecomment-2473437295  

Yeah I know I can throw. But it seems like another work around. Haven't done c++ for a while so don't know the limitations or if its possible at all. coming straight from python asyncio it just feels a bit arkward. Implementing stuff like database queries or paginated apis with generators seems kind a natural. Having to add these null checks or try/catch for a pretty simple case like query with no results is just a bit annoying :).
