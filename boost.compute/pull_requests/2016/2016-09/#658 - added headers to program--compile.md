# #658 added headers to program::compile [Merged]

> Username: Slonegg  
> Created at: 2016-09-14 20:59:51 UTC  
> Updated at: 2017-03-29 19:31:22 UTC  
> Merged at: 2017-03-29 19:31:22 UTC  
> Closed at: 2017-03-29 19:31:22 UTC  
> Url: https://github.com/boostorg/compute/pull/658  



---

## Comment 1

> Username: coveralls  
> Created_at: 2016-09-14 23:44:13 UTC  
> Url: https://github.com/boostorg/compute/pull/658#issuecomment-247190798  

[![Coverage Status](https://coveralls.io/builds/7892663/badge)](https://coveralls.io/builds/7892663)  
  
Coverage increased (+0.02%) to 83.357% when pulling **e845a80ab8bc4c25bcf4f086e030d1ee43b96ed0 on Slonegg:compile_program_with_headers** into **86211062a771ab0b494116b4223b63a292ad7d11 on boostorg:develop**.

---

## Comment 2

> Username: Slonegg  
> Created_at: 2016-11-21 18:03:07 UTC  
> Url: https://github.com/boostorg/compute/pull/658#issuecomment-262017548  

@kylelutz do you think we can add support for OpenCL headers to boost::compute? They're convenient if you include them in multiple files. Caching would be nice too...

---

## Comment 3

> Username: jszuppe  
> Created_at: 2017-03-25 17:31:52 UTC  
> Url: https://github.com/boostorg/compute/pull/658#issuecomment-289226714  

>Caching would be nice too...  
  
I think for caching result of `clCompileProgram(..)` you would need to store program source hash in `boost::compute::program` class.

---

## Comment 4

> Username: keryell  
> Created_at: 2017-03-29 17:33:48 UTC  
> Url: https://github.com/boostorg/compute/pull/658#issuecomment-290164346  

Yes.  
But is this an issue? The hash is only 160 bits.  
  
But more difficult seems to figure out what is really included by the compiler itself, which is outside of the Boost.Compute realm...  
This will depend on the options passed to the compiler that we need to understand, such as the `-I` paths and so on...  
So the best place to do this kind of caching is inside the OpenCL stack itself, such as done by PoCL or nVidia.

---

## Comment 5

> Username: Slonegg  
> Created_at: 2017-03-29 18:06:46 UTC  
> Url: https://github.com/boostorg/compute/pull/658#issuecomment-290174288  

I guess including all headers, compilations options and source of the program in hash should be more or less robust solution. Maybe warn user if he uses -I option, because it may trick caching system.

---

## Comment 6

> Username: jszuppe  
> Created_at: 2017-03-29 19:31:06 UTC  
> Url: https://github.com/boostorg/compute/pull/658#issuecomment-290199917  

>This will depend on the options passed to the compiler that we need to understand, such as the -I paths and so on...  
So the best place to do this kind of caching is inside the OpenCL stack itself, such as done by PoCL or nVidia.  
  
We already do offline-caching of OpenCL programs... so I imagine even right now you can pass `-I` option to build options and shoot yourself in foot by changing file/s you included. I think he only way to make Boost.Compute caching 100% robust would be to base hash not on program source, but on IL or binary. Unfortunately, binaries or IL are usually much bigger.  
  
Anyway, MR looks ok. I'll merge it. Thanks!

---
