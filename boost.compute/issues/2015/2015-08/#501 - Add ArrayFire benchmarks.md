# #501 - Add ArrayFire benchmarks [Open]

> Username: svenstaro  
> Created at: 2015-08-25 07:52:19 UTC  
> Updated at: 2017-04-25 15:04:07 UTC  
> Url: https://github.com/boostorg/compute/issues/501  

I think it would be interesting to see [ArrayFire](http://arrayfire.com/) benchmarks especially because it claims to be (mostly) faster than boost.compute in their [benchmarks](http://arrayfire.com/benchmarking-parallel-vector-libraries/). These could run as part of your other perf tests and give a better overall picture where boost.compute fits in performance-wise.  
##

---

## Comment 1

> Username: pavanky  
> Created at: 2015-08-25 09:43:52 UTC  
> Url: https://github.com/boostorg/compute/issues/501#issuecomment-134540812  

The benchmark code used for the blog can be seen here: https://github.com/arrayfire/valhalla. I am trying to make it a full blown benchmarking suite for all the libraries used in that blog post.   
  
@kylelutz If you are thinking about doing this let me know if you need any help from my end.

---

## Comment 2

> Username: VincentSC  
> Created at: 2015-09-16 12:52:47 UTC  
> Url: https://github.com/boostorg/compute/issues/501#issuecomment-140732689  

Any update on this? And any tips for Boost.Compute how to make it faster?

---

## Comment 3

> Username: pavanky  
> Created at: 2015-09-16 14:15:11 UTC  
> Url: https://github.com/boostorg/compute/issues/501#issuecomment-140753955  

@VincentSC One of the things I have noticed where ArrayFire is faster is because of the memory manager. If you can modify your local boost compute to implement even a rudimentary memory manager it can improve the performance quite a bit at smaller sizes.

---

## Comment 4

> Username: kylelutz  
> Created at: 2015-09-20 23:45:21 UTC  
> Url: https://github.com/boostorg/compute/issues/501#issuecomment-141845703  

Yeah, I'm hesitant to add more benchmark applications directly to Boost.Compute. I more like the `valhalla` approach to providing a single, separate benchmark suite which compares a variety of GPU libraries. This seems preferable to each individual library maintaining their own benchmark suite for every other library.  
  
And a memory manager would be good to have, especially for algorithms that could re-use the same "scratch" memory buffers over and over.

---

## Comment 5

> Username: svenstaro  
> Created at: 2015-11-27 11:29:18 UTC  
> Url: https://github.com/boostorg/compute/issues/501#issuecomment-160119328  

In that case maybe you should link to an external suite of benchmarks instead of providing your own and then work with the maintainer of that benchmark suite?
