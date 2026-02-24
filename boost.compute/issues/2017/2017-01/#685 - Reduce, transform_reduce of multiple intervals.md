# #685 - Reduce, transform_reduce of multiple intervals [Closed]

> Username: dPavelDev  
> Created at: 2017-01-14 17:01:21 UTC  
> Updated at: 2017-03-22 17:49:47 UTC  
> Closed at: 2017-03-22 17:49:47 UTC  
> Url: https://github.com/boostorg/compute/issues/685  

It is possible to efficiently reduce or transform_reduce of multiple intervals in array? Distances between begin and end of iterators in interval may be different, intervals hasn't intersections.

---

## Comment 1

> Username: jszuppe  
> Created at: 2017-01-14 17:20:41 UTC  
> Url: https://github.com/boostorg/compute/issues/685#issuecomment-272638202  

I think that currently the best option is to perform a scan and pick values  
that you want from the result. However, you'll have to benchmark if it's  
faster than performing multiple reduce operations. Otherwise, you'll have  
to modify reduce algorithm.  
  
14 sty 2017 18:01 "Pavel Darashkevich" <notifications@github.com>  
napisał(a):  
  
> It is possible to efficiently reduce or transform_reduce of multiple  
> intervals in array? Distances between begin and end of iterators in  
> interval may be different, intervals hasn't intersections.  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/compute/issues/685>, or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/ACmCBEZZCjYCVhhgeEew5KCF0hYaZ0tlks5rSP9igaJpZM4LjrdN>  
> .  
>
