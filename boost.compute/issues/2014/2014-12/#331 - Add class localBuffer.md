# #331 - Add class localBuffer [Closed]

> Username: adamcavendish  
> Created at: 2014-12-06 11:12:41 UTC  
> Updated at: 2014-12-25 15:45:12 UTC  
> Closed at: 2014-12-25 15:45:12 UTC  
> Url: https://github.com/boostorg/compute/issues/331  

Maybe we can add a localBuffer class which is just a simple wrapper for a std::size_t as a local buffer's size, which will be useful for kernel arguments for `__local *`.  
  
```  
const static char source[] = BOOST_COMPUTE_STRINGIZE_SOURCE(  
    __kernel void test(__local float * ptr) {  
       // ...  
    });  
compute::localBuffer<float> lbuf(1024);  
kernel.set_arg(0, lbuf); /// which can just be implemented easily as: kernel.set_arg(0, lbuf.size(), nullptr)  
```

---

## Comment 1

> Username: roshanrags  
> Created at: 2014-12-06 15:27:30 UTC  
> Url: https://github.com/boostorg/compute/issues/331#issuecomment-65901564  

Isn't local memory declared inside kernel code? Something like  
  
```  
const static char source[] = BOOST_COMPUTE_STRINGIZE_SOURCE(  
    __kernel void test() {  
       __local float ptr[1024];  
    });  
```

---

## Comment 2

> Username: adamcavendish  
> Created at: 2014-12-06 16:44:17 UTC  
> Url: https://github.com/boostorg/compute/issues/331#issuecomment-65904714  

@roshanr95   
  
if you declare local memory inside the kernel code, it must be in constant size. However, in many ways, dynamic allocated local memory is needed though this way.

---

## Comment 3

> Username: kylelutz  
> Created at: 2014-12-06 20:09:45 UTC  
> Url: https://github.com/boostorg/compute/issues/331#issuecomment-65911800  

This is a cool idea. I'll try to figure out how best to fit it into the API.

---

## Comment 4

> Username: roshanrags  
> Created at: 2014-12-07 08:54:02 UTC  
> Url: https://github.com/boostorg/compute/issues/331#issuecomment-65930945  

@adamcavendish Cool!

---

## Comment 5

> Username: kylelutz  
> Created at: 2014-12-24 22:08:44 UTC  
> Url: https://github.com/boostorg/compute/issues/331#issuecomment-68076277  

I added an implementation of this in PR #355. Could you test it out and let me know what you think?

---

## Comment 6

> Username: adamcavendish  
> Created at: 2014-12-25 15:45:12 UTC  
> Url: https://github.com/boostorg/compute/issues/331#issuecomment-68106612  

@kylelutz This is great. Thx!
