# #75 - Dynamic cast causes code to fail to compile with -fno-rtti on gcc [Closed]

> Username: edtanous  
> Created at: 2020-04-16 17:17:15 UTC  
> Updated at: 2020-04-17 16:33:50 UTC  
> Closed at: 2020-04-17 16:27:36 UTC  
> Url: https://github.com/boostorg/json/issues/75  

https://github.com/CPPAlliance/json/blob/ad1a3378b6caac61da44bec403336a02d3fd381f/include/boost/json/detail/default_resource.hpp#L47  
  
The above line prevents compilation if -fno-rtti is enabled in gcc (version 9, but I suspect that doesn't matter)  
  
In member function ‘virtual bool boost::json::detail::default_resource::do_is_equal(const boost::container::pmr::memory_resource&) const’:  
...boost/json/detail/default_resource.hpp:48:41: error: ‘dynamic_cast’ not permitted with ‘-fno-rtti’  
  
This appears to be a regression on this commit:  
https://github.com/CPPAlliance/json/commit/a47b0f3fc16980afa644f6f3c0d62aa11e687e4c  
  
Backing up to one commit previous allows the same code to build and link just fine.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-04-16 17:40:54 UTC  
> Url: https://github.com/boostorg/json/issues/75#issuecomment-614795941  

Yeah I always forget about `-fno-rtti`. How do you suggest to fix it?

---

## Comment 2

> Username: edtanous  
> Created at: 2020-04-16 17:43:43 UTC  
> Url: https://github.com/boostorg/json/issues/75#issuecomment-614797450  

I don't understand enough about what that code is doing to render an opinion for how to fix it.  I'll try to dig deeper in the next day or two to be able to speak intelligently about it and put up a PR.  
  
I was hoping it was something easy :)

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-04-16 17:45:58 UTC  
> Url: https://github.com/boostorg/json/issues/75#issuecomment-614798604  

>I don't understand enough about what that code is doing to render an opinion for how to fix it.  
  
Well, I think we need to do two things:  
  
1. Determine if RTTI is turned off (some kind of ifdef or compiler setting), and  
2. Decide how we want the function to behave with RTTI off. For example we could always return `false`.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-04-16 17:46:15 UTC  
> Updated at: 2020-04-16 17:47:41 UTC  
> Url: https://github.com/boostorg/json/issues/75#issuecomment-614798749  

Can we simply just always return `false` here? Or perhaps:  
```  
bool do_is_equal(memory_resource const& mr) const noexcept  
{  
    return this == &mr;  
}  
```

---

## Comment 5

> Username: edtanous  
> Created at: 2020-04-17 16:33:30 UTC  
> Updated at: 2020-04-17 16:33:50 UTC  
> Url: https://github.com/boostorg/json/issues/75#issuecomment-615344525  

Pulled  cb281d6 down and rebuilt.  Issue solved.  Thanks for the quick turnaround and the great work on this library.  It really does seem to solve some problems.
