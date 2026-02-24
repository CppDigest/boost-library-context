# #103 - Wrong use of std::uniform_int_distribution [Closed]

> Username: ishitatsuyuki  
> Created at: 2016-12-04 12:31:43 UTC  
> Updated at: 2016-12-05 08:53:30 UTC  
> Closed at: 2016-12-04 13:01:45 UTC  
> Url: https://github.com/boostorg/fiber/issues/103  

`std::uniform_int_distribution` is a stateful class. Please do not initialize it at every random call.  
  
Example:  
https://github.com/boostorg/fiber/blob/master/src/algo/work_stealing.cpp#L61

---

## Comment 1

> Username: olk  
> Created at: 2016-12-04 13:01:45 UTC  
> Url: https://github.com/boostorg/fiber/issues/103#issuecomment-264702655  

The distributions like std::uniform_real_distribution can be as short-lived as you want. They're plain functors where the constructor either has no arguments or only stores the arguments for when the functor gets executed.

---

## Comment 2

> Username: ishitatsuyuki  
> Created at: 2016-12-04 13:06:11 UTC  
> Url: https://github.com/boostorg/fiber/issues/103#issuecomment-264702865  

Not sure if I understood what you mean, but `std::uniform_int_distribution` definitely has internal states. If you are discarding them every time, you will get the wrong behavior.

---

## Comment 3

> Username: olk  
> Created at: 2016-12-04 13:12:24 UTC  
> Updated at: 2016-12-04 13:13:22 UTC  
> Url: https://github.com/boostorg/fiber/issues/103#issuecomment-264703190  

As Stephen T. Lavavej (member of the standard committee) suggested at GoingNative 2013 to simply create new distributions as it is required (for instance variation of the distribution range) - it shouldn't be a performance issue.  
  
I saw this pattern often in implementations of spin locks with backoff.

---

## Comment 4

> Username: ishitatsuyuki  
> Created at: 2016-12-04 13:15:56 UTC  
> Url: https://github.com/boostorg/fiber/issues/103#issuecomment-264703359  

If you do that, you are losing the uniform part; it's simply a alphabet-only way of mod(%). Since keeping it doesn't incur much overhead too, why don't you consider storing it statically?

---

## Comment 5

> Username: olk  
> Created at: 2016-12-04 13:20:23 UTC  
> Url: https://github.com/boostorg/fiber/issues/103#issuecomment-264703586  

```  
static thread_local std::minstd_rand generator;  
	std::size_t idx = 0, max_idx = 8;  
	for ( int i = 0; i < 10; ++i) {  
		std::cout << i << ": " << std::uniform_int_distribution< std::size_t >{ 0, max_idx }( generator) << std::endl;  
	}  
```  
  
0: 0  
1: 0  
2: 5  
3: 8  
4: 8  
5: 1  
6: 4  
7: 3  
8: 2  
9: 6

---

## Comment 6

> Username: ishitatsuyuki  
> Created at: 2016-12-05 08:36:12 UTC  
> Url: https://github.com/boostorg/fiber/issues/103#issuecomment-264795178  

Seems you're right; the internal implementation most time doesn't rely on the state.  
  
However, it's not considered a good practice to rely on implementation details; also, I think it's better to do this instead of spin attacking:  
```  
if(idx>=idx_) ++idx; // Note that the max should be reduced by 1  
```

---

## Comment 7

> Username: olk  
> Created at: 2016-12-05 08:53:30 UTC  
> Url: https://github.com/boostorg/fiber/issues/103#issuecomment-264798258  

In my tests simple increment of idx was less performant than using random numbers
