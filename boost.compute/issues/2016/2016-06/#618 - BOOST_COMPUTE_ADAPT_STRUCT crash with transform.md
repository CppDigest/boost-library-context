# #618 - BOOST_COMPUTE_ADAPT_STRUCT crash with transform [Closed]

> Username: Naviee  
> Created at: 2016-06-21 12:43:53 UTC  
> Updated at: 2016-07-24 11:33:13 UTC  
> Closed at: 2016-07-24 11:33:08 UTC  
> Url: https://github.com/boostorg/compute/issues/618  

This code always crashes once transform is called.  
  
```  
BOOST_COMPUTE_FUNCTION(bool, modify, (test::clParticle a),  
{  
    a.y += 0.5;  
    return true;  
});  
  
boost::compute::transform   (gpu_particles.begin(), gpu_particles.end(), gpu_particles.begin(), modify,   
```

---

## Comment 1

> Username: Naviee  
> Created at: 2016-06-21 13:20:27 UTC  
> Url: https://github.com/boostorg/compute/issues/618#issuecomment-227436993  

Btw. I get a "Build Programm failure" exception when debugging the whole thing.

---

## Comment 2

> Username: jszuppe  
> Created at: 2016-06-21 13:23:06 UTC  
> Url: https://github.com/boostorg/compute/issues/618#issuecomment-227437722  

Try  
  
```  
BOOST_COMPUTE_FUNCTION(bool, modify, (clParticle a),  
{  
    a.y += 0.5;  
    return true;  
});  
```

---

## Comment 3

> Username: jszuppe  
> Created at: 2016-06-21 13:25:19 UTC  
> Url: https://github.com/boostorg/compute/issues/618#issuecomment-227438314  

Also `BOOST_COMPUTE_ADAPT_STRUCT(test::clParticle, clParticle, (x, y, z, ox, oy, oz, vx, vy, vz)` is missing one bracket at the end.

---

## Comment 4

> Username: Naviee  
> Created at: 2016-06-21 13:27:16 UTC  
> Url: https://github.com/boostorg/compute/issues/618#issuecomment-227438874  

Thank you haahh but this does not compile anymore then because clParticle is undeclared (makes sense because it's not in the global namespace right?!)  
  
Concerning your second note, thanks, I fixed this in the issue code but in my own code it's correctly there.

---

## Comment 5

> Username: jszuppe  
> Created at: 2016-06-21 13:28:01 UTC  
> Url: https://github.com/boostorg/compute/issues/618#issuecomment-227439060  

In the transform operation you are trying to assign `bool` to `clParticle`, that's not right.

---

## Comment 6

> Username: jszuppe  
> Created at: 2016-06-21 13:28:49 UTC  
> Updated at: 2016-06-21 13:34:12 UTC  
> Url: https://github.com/boostorg/compute/issues/618#issuecomment-227439297  

Yes, sorry. My first comment is wrong.  
Edit: I was thinking that the type in the `BOOST_COMPUTE_FUNCTION` should have the same name as in `BOOST_COMPUTE_ADAPT_STRUCT`, but I was wrong. Nevermind that ;)

---

## Comment 7

> Username: jszuppe  
> Created at: 2016-06-21 13:29:46 UTC  
> Updated at: 2016-06-21 13:31:39 UTC  
> Url: https://github.com/boostorg/compute/issues/618#issuecomment-227439570  

Your function should look like this:  
  
``` cpp  
BOOST_COMPUTE_FUNCTION(test::clParticle, modify, (test::clParticle a),  
{  
    a.y += 0.5;  
    return a;  
});  
```  
  
Edit:  
Also, change the order in the initializer list in the `test::clParticle` constructor.

---

## Comment 8

> Username: Naviee  
> Created at: 2016-06-21 13:32:24 UTC  
> Url: https://github.com/boostorg/compute/issues/618#issuecomment-227440332  

Darn, yes that makes sense! I didn't have in mind that the result is assigned. Totally my fault! I will try that right away.

---

## Comment 9

> Username: jszuppe  
> Created at: 2016-06-21 13:38:30 UTC  
> Url: https://github.com/boostorg/compute/issues/618#issuecomment-227442089  

When you get "Build Programm failure" error, try compiling with option   
`-DBOOST_COMPUTE_DEBUG_KERNEL_COMPILATION`. That way, Boost.Compute will print the build log and you'll see what's wrong.

---

## Comment 10

> Username: Naviee  
> Created at: 2016-06-21 13:41:15 UTC  
> Url: https://github.com/boostorg/compute/issues/618#issuecomment-227442895  

Thanks! With the correct return type the crash is gone. :) And thanks for the tip about the BOOST_COMPUTE_DEBUG_KERNEL_COMPILATION option. Will do!  
  
Cheers

---

## Comment 11

> Username: jszuppe  
> Created at: 2016-07-24 11:26:11 UTC  
> Url: https://github.com/boostorg/compute/issues/618#issuecomment-234771980  

Hi Naviee, why did you reopen this issue?

---

## Comment 12

> Username: Naviee  
> Created at: 2016-07-24 11:33:08 UTC  
> Url: https://github.com/boostorg/compute/issues/618#issuecomment-234772248  

Argh, sorry, I had a problem with the code after I updated to the latest version of this repository but then I noticed an error in my code. At that time I had already reopened it and forgot to close it again. Please ignore it (not me please.. :).
