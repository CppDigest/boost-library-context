# #218 - scale_add_vec test shouldn't require opencl 1.2 [Closed]

> Username: kylelutz  
> Created at: 2014-08-08 02:24:31 UTC  
> Updated at: 2014-08-09 16:00:00 UTC  
> Closed at: 2014-08-09 16:00:00 UTC  
> Url: https://github.com/boostorg/compute/issues/218  

The `scale_add_vec` test in `test_closure.cpp` is marked `REQUIRES_OPENCL_VERSION(1,2)`  but it doesn't look like it requires any OpenCL 1.2 features.  
  
See: https://github.com/kylelutz/compute/blob/master/test/test_closure.cpp#L69  
  
@ddemidov thoughts on this? Do you remember why we marked it as requiring 1.2?

---

## Comment 1

> Username: ddemidov  
> Created at: 2014-08-09 11:45:28 UTC  
> Url: https://github.com/boostorg/compute/issues/218#issuecomment-51684495  

Frankly, I have no idea why I marked `scale_add_vec` as requiring 1.2. Both the current master and the  201f85a work fine on Tesla K40 with the line in question removed. #226 should fix this. Sorry for the misunderstanding!

---

## Comment 2

> Username: kylelutz  
> Created at: 2014-08-09 15:59:59 UTC  
> Url: https://github.com/boostorg/compute/issues/218#issuecomment-51690773  

Yeah, I was more curious then anything. Thanks for fixing and testing this!
