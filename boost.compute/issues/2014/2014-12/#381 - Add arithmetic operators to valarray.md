# #381 - Add arithmetic operators to valarray [Closed]

> Username: kylelutz  
> Created at: 2014-12-29 19:07:34 UTC  
> Updated at: 2015-07-08 04:39:19 UTC  
> Closed at: 2015-07-08 04:39:17 UTC  
> Url: https://github.com/boostorg/compute/issues/381  

Add overloads for the arithmetic operators (e.g. `+`, `-`) to `boost::compute::valarray<T>` to match `std::valarray<T>`.  
  
From the review:  
  
```  
- <http://kylelutz.github.io/compute/boost/compute/valarray.html> -  
presumably compute::valarray has overloaded arithmetic operators?  Are  
they mentioned anywhere?  
```

---

## Comment 1

> Username: guptadivyansh  
> Created at: 2015-01-06 16:21:04 UTC  
> Url: https://github.com/boostorg/compute/issues/381#issuecomment-68887871  

http://www.cplusplus.com/reference/valarray/valarray/operators/  
  
Similar to these?

---

## Comment 2

> Username: kylelutz  
> Created at: 2015-01-06 16:28:13 UTC  
> Url: https://github.com/boostorg/compute/issues/381#issuecomment-68888996  

@guptadivyansh yeah, those are the ones.

---

## Comment 3

> Username: guptadivyansh  
> Created at: 2015-01-06 19:18:57 UTC  
> Url: https://github.com/boostorg/compute/issues/381#issuecomment-68917107  

https://github.com/guptadivyansh/compute/commit/1771ad07b9dd3f893e2497f6b17ece46ee391c85  
  
@kylelutz Can you please look at this?

---

## Comment 4

> Username: kylelutz  
> Created at: 2015-01-07 06:32:46 UTC  
> Url: https://github.com/boostorg/compute/issues/381#issuecomment-68984420  

So that will actually perform the operation on the host rather than on the compute device (e.g. GPU). For implementing these functions I'd probably use the [`boost::compute::transform()`](http://kylelutz.github.io/compute/boost/compute/transform.html) algorithm.

---

## Comment 5

> Username: archangel4  
> Created at: 2015-01-07 10:10:04 UTC  
> Url: https://github.com/boostorg/compute/issues/381#issuecomment-69001919  

Could you provide some more reference to implement this feature ?

---

## Comment 6

> Username: kylelutz  
> Created at: 2015-01-08 03:48:39 UTC  
> Updated at: 2015-01-08 03:49:57 UTC  
> Url: https://github.com/boostorg/compute/issues/381#issuecomment-69132294  

A basic implementation would be something like:  
  
```  
valarray<T> operator+(const valarray<T> &other) const  
{  
    const context &context = m_buffer.get_context();  
    command_queue queue(context, context.get_device());  
    valarray<T> result(size(), context);  
    transform(begin(), end(), other.begin(), plus<T>(), queue);  
    queue.finish();  
    return result;  
}  
```  
  
As operator overloads don't allow the `command_queue` argument to be passed in, we must create one explicitly to perform the `transform()` operation. Because of that, actually using these operators would not be very efficient. For that reason I am a bit hesitant to add them, but I do see how they could be useful as a convenience.

---

## Comment 7

> Username: jszuppe  
> Created at: 2015-05-07 18:26:35 UTC  
> Updated at: 2015-05-07 18:31:57 UTC  
> Url: https://github.com/boostorg/compute/issues/381#issuecomment-99968711  

Logical not operator `valarray<bool> operator!() const;` cannot be implemented as in OpenCL it's impossible to build memory buffer with bool type, which makes valarray<bool> incorrect. However, I can implement `valarray<char> operator!() const;` operator (or with int). Same issue with other logical operators (&&, >, <, etc.)

---

## Comment 8

> Username: kylelutz  
> Created at: 2015-05-08 03:09:54 UTC  
> Url: https://github.com/boostorg/compute/issues/381#issuecomment-100084460  

Yeah, I'd just use `valarray<char>`.

---

## Comment 9

> Username: kylelutz  
> Created at: 2015-07-08 04:39:17 UTC  
> Url: https://github.com/boostorg/compute/issues/381#issuecomment-119428795  

Implemented in PR #454.
