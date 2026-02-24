# #241 Nbody example improved [Merged]

> Username: f-koehler  
> Created at: 2014-08-19 21:32:07 UTC  
> Updated at: 2014-08-20 08:26:45 UTC  
> Merged at: 2014-08-20 02:07:03 UTC  
> Closed at: 2014-08-20 02:07:03 UTC  
> Url: https://github.com/boostorg/compute/pull/241  

The Nbody example has been improved as follows:  
- command line options for both particle number and step width  
- removed unused variables  
- data is aligned using float4 (also I do not see a big performance improvement)

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-08-19 21:55:13 UTC  
> Url: https://github.com/boostorg/compute/pull/241#issuecomment-52704843  

[![Coverage Status](https://coveralls.io/builds/1105411/badge)](https://coveralls.io/builds/1105411)  
  
Coverage remained the same when pulling **f91a56208d1b0e32de1b3b8cd69d9c3ffe1a7153 on f-koehler:nbody** into **417a50e3f3245c95167aa8341a81a92090a815be on kylelutz:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-08-20 02:01:10 UTC  
> Url: https://github.com/boostorg/compute/pull/241#discussion_r16455578  

these lines could be replaced with `float4 r = (float4)(0, 0, 0, 0);`

---

## Comment 3

> Username: kylelutz  
> Created_at: 2014-08-20 02:01:38 UTC  
> Url: https://github.com/boostorg/compute/pull/241#discussion_r16455587  

cool! this looks a lot simpler.

---

## Comment 4

> Username: kylelutz  
> Created_at: 2014-08-20 02:05:17 UTC  
> Url: https://github.com/boostorg/compute/pull/241#discussion_r16455662  

this kernel could be replaced with the [`fill()`](http://kylelutz.github.io/compute/boost/compute/fill.html) algorithm.  
  
something like:  
  
```  
boost::compute::fill_n(  
    compute::make_buffer_iterator<float4_>(m_velocity, 0),  
    m_particles,  
    float4_(0, 0, 0, 0),  
    m_queue  
);  
```

---

## Comment 5

> Username: kylelutz  
> Created_at: 2014-08-20 02:07:12 UTC  
> Url: https://github.com/boostorg/compute/pull/241#issuecomment-52725829  

Looks good!  
  
I also left a few notes. Thanks for updating this!

---

## Comment 6

> Username: kylelutz  
> Created_at: 2014-08-20 02:10:20 UTC  
> Url: https://github.com/boostorg/compute/pull/241#issuecomment-52725987  

Another thing to look into is using the Boost.Compute random number generators to initialize the positions on the GPU instead of generating them on the CPU and then transferring them.

---

## Comment 7

> Username: f-koehler  
> Created_at: 2014-08-20 07:22:23 UTC  
> Url: https://github.com/boostorg/compute/pull/241#issuecomment-52742126  

The things you mentioned where mainly features of Boost.Compute that I was not aware of yet. I'll have a look at it.

---
