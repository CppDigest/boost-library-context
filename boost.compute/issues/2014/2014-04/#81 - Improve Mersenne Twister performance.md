# #81 - Improve Mersenne Twister performance [Open]

> Username: etam  
> Created at: 2014-04-07 19:16:07 UTC  
> Updated at: 2017-09-25 16:25:05 UTC  
> Url: https://github.com/boostorg/compute/issues/81  

On my machine generating 1024\*1024\*32 random numbers takes about 10 seconds.  
  
Here: http://www.fixstars.com/en/opencl/book/OpenCLProgrammingBook/mersenne-twister/ (and complete downloads here http://www.fixstars.com/en/opencl/book/sample/) you can find implementation that does the same amount of work in about 0.35s.  
##

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-04-08 16:13:35 UTC  
> Url: https://github.com/boostorg/compute/issues/81#issuecomment-39868201  

Thanks for the report and the links. I'll try to find some time to take a look and update the code.

---

## Comment 2

> Username: etam  
> Created at: 2014-04-13 13:50:49 UTC  
> Url: https://github.com/boostorg/compute/issues/81#issuecomment-40308029  

Well. After digging deeper, I found that the implementation there is not the best solution. The "official" one http://www.math.sci.hiroshima-u.ac.jp/~m-mat/MT/MTGP/index.html should be used instead.

---

## Comment 3

> Username: kylelutz  
> Created at: 2014-04-17 02:19:39 UTC  
> Url: https://github.com/boostorg/compute/issues/81#issuecomment-40674169  

Interesting. We should be able to integrate their algorithm (though perhaps as another engine, `mtgp_engine`).

---

## Comment 4

> Username: dacmot  
> Created at: 2017-09-22 19:58:00 UTC  
> Updated at: 2017-09-22 20:14:22 UTC  
> Url: https://github.com/boostorg/compute/issues/81#issuecomment-331545987  

I'm also very interested in this. On my machine (12 CPU cores + GTX 680) 10MB of uniform_real_distribution with mersenne_twister_engine takes about 1.6 seconds to generate, which is better than for @etam, but still very slow. I'm wondering if the fact that the mersenne_twister_engine code creates a second temporary vector and does two transforms instead of composing the scaling kernel could have something to do with it. Is it even possible to compose kernels with boost::compute?  
  
Also, I was wondering what were the developers' thoughts on adding more engines. Doing a search on GPU random number generations I stumbled on a few including https://github.com/clMathLibraries/clRNG and http://cas.ee.ic.ac.uk/people/dt10/research/rngs-gpu-uniform.html. The MWC64X one is of particular interest for me as I don't need an extremely long period but performance is much more important. Licenses are BSD.

---

## Comment 5

> Username: jszuppe  
> Created at: 2017-09-22 20:44:21 UTC  
> Url: https://github.com/boostorg/compute/issues/81#issuecomment-331555988  

I'd recommend improving current Philox implementation. Right now in Boost.Compute it's designed badly and has poor performance, It can be improved to achieve 200 - 350 GB/s (50 - 90 GSamples/s)  on modern top GPUs (depending on GPU and it's architecture). It's really simple RNG. You can also implement XORWOW, which should achieve similar or higher performance.

---

## Comment 6

> Username: dacmot  
> Created at: 2017-09-25 16:17:31 UTC  
> Url: https://github.com/boostorg/compute/issues/81#issuecomment-331933655  

There's a Philox implementation? I only see a ThreeFry and a linear congruential along with the MT engine. Also the ThreeFry is not in the API overview and doesn't compile when used in conjunction with uniform_real_distribution since its generate() method doesn't take an scaling kernel.

---

## Comment 7

> Username: jszuppe  
> Created at: 2017-09-25 16:25:05 UTC  
> Url: https://github.com/boostorg/compute/issues/81#issuecomment-331935872  

Oh, sorry, my mistake, indeed it's ThreeFry. Nonetheless, it's fast. I think that adding Philox to Boost.Compute is the best option to have fast random number generator, and should not be so hard. Unfortunately, recently I don't have enough free time to do it.
