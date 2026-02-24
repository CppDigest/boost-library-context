# #25 Fixed unused parameters warnings [Closed]

> Username: Kojoley  
> Created at: 2016-09-01 16:05:04 UTC  
> Updated at: 2018-01-03 16:05:42 UTC  
> Closed at: 2018-01-03 16:01:38 UTC  
> Url: https://github.com/boostorg/random/pull/25  

Fixes following warnings:  
  
```  
In file included from ../../../boost/random.hpp:63:  
../../../boost/random/discrete_distribution.hpp:95:60: warning: unused parameter 'average' [-Wunused-parameter]  
    static WeightType normalize(WeightType val, WeightType average)  
                                                           ^  
../../../boost/random/discrete_distribution.hpp:186:66: warning: unused parameter 'weights' [-Wunused-parameter]  
    static WeightType try_get_sum(const std::vector<WeightType>& weights)  
                                                                 ^  
```

---

## Comment 1

> Username: Romain-Geissler-1A  
> Created_at: 2017-06-18 17:52:28 UTC  
> Url: https://github.com/boostorg/random/pull/25#issuecomment-309292265  

Ping.

---

## Comment 2

> Username: jeking3  
> Created_at: 2017-10-08 12:11:05 UTC  
> Url: https://github.com/boostorg/random/pull/25#issuecomment-335002375  

@swatanabe I made the same changes locally to resolve these warnings; however I didn't comment out the variable name, I just removed the variable name.  Easy fix for a couple of warnings...

---

## Comment 3

> Username: Romain-Geissler  
> Created_at: 2017-11-05 16:50:32 UTC  
> Url: https://github.com/boostorg/random/pull/25#issuecomment-341987314  

Ping @swatanabe

---

## Comment 4

> Username: Kojoley  
> Created_at: 2017-11-20 23:21:07 UTC  
> Url: https://github.com/boostorg/random/pull/25#issuecomment-345864503  

It looks like this will never be merged.

---

## Comment 5

> Username: jeking3  
> Created_at: 2017-11-21 02:16:59 UTC  
> Url: https://github.com/boostorg/random/pull/25#issuecomment-345894792  

@Kojoley We're in the middle of the boost 1.67 release cycle so I wouldn't expect anything to be merged right now.

---

## Comment 6

> Username: Kojoley  
> Created_at: 2017-12-26 12:43:07 UTC  
> Url: https://github.com/boostorg/random/pull/25#issuecomment-353964658  

@swatanabe If the reason of not merging this is because you prefer not commenting argument names but removing them just say me and I will do it.

---
