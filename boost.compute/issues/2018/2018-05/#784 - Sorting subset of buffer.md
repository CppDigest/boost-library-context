# #784 - Sorting subset of buffer [Open]

> Username: cdeterman  
> Created at: 2018-05-22 16:08:35 UTC  
> Updated at: 2020-07-13 05:40:14 UTC  
> Url: https://github.com/boostorg/compute/issues/784  

Not sure of a mailing list so I hope posting a use question here is appropriate.  I have been able to use `boost::compute::sort` successfully on a general OpenCL buffer.  However, I am often dealing with 'padded' buffers that are interpreted as matrices (row-wise storage).  For example, the matrix  
  
```  
8 2 7  
6 5 4  
1 3 9  
```  
  
Is actually stored as  
  
```  
8 2 7 0 0 0  
6 5 4 0 0 0  
1 3 9 0 0 0   
```  
  
So the internal flat buffer would be  
  
```  
8 2 7 0 0 0 6 5 4 0 0 0 1 3 9 0 0 0  
```  
  
How could I use `sort` on just the 'non-padded' elements?  
  
Likewise, the column wise may also be padded (less common but might as well include here)  
  
```  
8 2 7 0 0 0  
6 5 4 0 0 0  
1 3 9 0 0 0   
0 0 0 0 0 0  
0 0 0 0 0 0   
0 0 0 0 0 0  
```

---

## Comment 1

> Username: jszuppe  
> Created at: 2018-05-22 16:30:59 UTC  
> Updated at: 2018-05-22 16:31:43 UTC  
> Url: https://github.com/boostorg/compute/issues/784#issuecomment-391055972  

If the padding is constant, then you should be able to develop something similar to `strided_iterator`, which would jump over the padding to the next row (and ignore column padding). I don't see a way to do this using included fancy iterators.

---

## Comment 2

> Username: cdeterman  
> Created at: 2018-05-22 18:35:26 UTC  
> Url: https://github.com/boostorg/compute/issues/784#issuecomment-391096128  

@jszuppe any thoughts on how to actually use `strided_iterator` to that effect?  My understanding, from what I can see in the test examples [here](https://github.com/boostorg/compute/blob/master/test/test_strided_iterator.cpp) is that it takes every 'x' element (e.g. second, third, etc.).

---

## Comment 3

> Username: jszuppe  
> Created at: 2018-05-22 18:38:33 UTC  
> Url: https://github.com/boostorg/compute/issues/784#issuecomment-391097120  

I meant you would have to implement your own iterator class similar to strided iterator.

---

## Comment 4

> Username: cdeterman  
> Created at: 2018-05-23 15:05:08 UTC  
> Updated at: 2018-05-23 15:05:26 UTC  
> Url: https://github.com/boostorg/compute/issues/784#issuecomment-391381643  

@jszuppe Are there any guidelines for how to approach this or should I just try to adapt from `strided_iterator.hpp`?  This would be my first attempt at contributing to boost compute.

---

## Comment 5

> Username: jszuppe  
> Created at: 2018-05-28 10:03:24 UTC  
> Url: https://github.com/boostorg/compute/issues/784#issuecomment-392482135  

I think `strided_iterator.hpp` is a good starting point. You need to change index expression. I don't have much free time to show you how to do it. Maybe I'll find a moment this week.

---

## Comment 6

> Username: elwongest  
> Created at: 2020-07-13 05:40:14 UTC  
> Url: https://github.com/boostorg/compute/issues/784#issuecomment-657369922  

I know this question has been asked a long time ago, but having just got to using this very cool library, maybe this will help.  
If your strided buffer is in host memory, you can move it to an "unstrided" device vector by using enqueue_write_buffer_rect instead of copy. You can "restride" it back to the host using enqueue_read_buffer_rect.  
I use these for pixel format conversion in and out of FFmpeg AVFrames
