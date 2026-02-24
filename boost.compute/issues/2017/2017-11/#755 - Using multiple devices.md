# #755 - Using multiple devices [Open]

> Username: cdeterman  
> Created at: 2017-11-30 19:31:52 UTC  
> Updated at: 2018-12-20 16:40:33 UTC  
> Url: https://github.com/boostorg/compute/issues/755  

I see there is a previously closed issue regarding multiple devices in a context [here](https://github.com/boostorg/compute/issues/233).  Does that imply that computations are applied across multiple devices or just that multiple devices are available to be used independently?  I can't quite tell from the documentation if leveraging multiple devices is implemented here.

---

## Comment 1

> Username: jszuppe  
> Created at: 2017-11-30 19:38:13 UTC  
> Url: https://github.com/boostorg/compute/issues/755#issuecomment-348297339  

It does not mean computation is performed on multiple devices. IMHO, the most important thing about this is that memory (buffers) allocated in an OpenCL context can be used on all devices within the same context. Boost.Compute context class is just a wrapper for OpenCL context. I encourage you to read OpenCL documentation and check what is the exact relationship between a context and a device in OpenCL.

---

## Comment 2

> Username: cdeterman  
> Created at: 2017-11-30 19:43:23 UTC  
> Url: https://github.com/boostorg/compute/issues/755#issuecomment-348298728  

The distinction is clear to me, I just wasn't sure what was implemented here.  I wanted to simply confirm whether or not the computations are not distributed across devices automatically.  I am interested in using multiple devices to scale computations even further than leveraging a single GPU.

---

## Comment 3

> Username: jszuppe  
> Created at: 2017-11-30 19:46:10 UTC  
> Url: https://github.com/boostorg/compute/issues/755#issuecomment-348299518  

I was working on distributed algorithms in here: https://github.com/boostorg/compute/pull/644, but after August 2016 I haven't had enough free time to refactor and finish it.

---

## Comment 4

> Username: cdeterman  
> Created at: 2017-11-30 19:51:13 UTC  
> Url: https://github.com/boostorg/compute/issues/755#issuecomment-348300948  

@jszuppe do you have a roadmap or anything delineating what else is required to finish it?  I am quite busy as well but perhaps I can lend a hand when I manage to have some availability.  IMHO this would be a very important addition that many would find useful.

---

## Comment 5

> Username: jszuppe  
> Created at: 2017-11-30 19:55:53 UTC  
> Url: https://github.com/boostorg/compute/issues/755#issuecomment-348302143  

What I need to finish it is distributed iterator class. The idea was to have a iterator class for multiple buffers which can be templated with fancy iterators that are in Boost.Compute to get a fancy iterator (transform, zip etc.) that works with multiple buffers. I would have to take a look at the code.

---

## Comment 6

> Username: lovingxiuxiu  
> Created at: 2018-12-19 02:11:33 UTC  
> Url: https://github.com/boostorg/compute/issues/755#issuecomment-448443897  

@cdeterman hi,Do you have any idea about how to use multiple GPUS?

---

## Comment 7

> Username: cdeterman  
> Created at: 2018-12-20 16:40:33 UTC  
> Url: https://github.com/boostorg/compute/issues/755#issuecomment-449059963  

@lovingxiuxiu I have seen multiple different implementations of specific things in different papers.  Hence my initial interest here where many things would be consolidated instead of implementing them all myself.  So I have a rough idea but I am by no means an expert.
