# #271 Mark fcontext asm functions as hidden visibility [Merged]

> Username: rdoeffinger  
> Created at: 2024-10-04 18:47:14 UTC  
> Updated at: 2024-10-09 06:47:47 UTC  
> Merged at: 2024-10-08 09:12:29 UTC  
> Closed at: 2024-10-08 09:12:29 UTC  
> Url: https://github.com/boostorg/context/pull/271  

These functions should not be exported as dynamic  
symbols if a library uses boost.  
  
Note: I have no idea which if any changes armasm, armclang or xcoff assembler targets would need. It is also very lightly tested, essentially only the arm64_aapcs_macho target at this point.  
So maybe more of a bug report/discussion point at this stage.

---

## Comment 1

> Username: dixyes  
> Created_at: 2024-10-05 23:30:58 UTC  
> Url: https://github.com/boostorg/context/pull/271#issuecomment-2395225740  

There are many softwares using boost context asm codes to implement their own context switching, this may be a huge break change for them.

---

## Comment 2

> Username: rdoeffinger  
> Created_at: 2024-10-06 06:30:12 UTC  
> Url: https://github.com/boostorg/context/pull/271#issuecomment-2395314041  

Are you maybe confusing it with the properly namespaced C++ functions like boost::context::make_fcontext? (I admit I can't find its definition right now, but that seems to be the one documented, not the C variant).  
Either way the name make_fcontext is far too generic to be acceptable to export, it would at least have to be something like boost_context_make_fcontext.

---

## Comment 3

> Username: dixyes  
> Created_at: 2024-10-06 08:57:29 UTC  
> Url: https://github.com/boostorg/context/pull/271#issuecomment-2395355598  

> Are you maybe confusing it with the properly namespaced C++ functions like boost::context::make_fcontext? (I admit I can't find its definition right now, but that seems to be the one documented, not the C variant). Either way the name make_fcontext is far too generic to be acceptable to export, it would at least have to be something like boost_context_make_fcontext.  
  
I agree this, but  
  
https://github.com/php/php-src/blob/39ae00fa0a72e7668b419399567c9709160e0bbb/Zend/zend_fibers.c#L176

---

## Comment 4

> Username: rdoeffinger  
> Created_at: 2024-10-07 12:14:45 UTC  
> Url: https://github.com/boostorg/context/pull/271#issuecomment-2396762699  

Any proposal how to approach it?  
The code you linked to has made a copy of the asm files, so they will not be affected (even if they update the code, they will actually profit from this change). Anyone linking statically will also not be affected.  
In fact, in both of these cases users will BENEFIT from this even further, as their internal copies are no longer at risk of being overridden by symbols provided by a dynamically linked libboost, which might be incompatible.  
Also as this example shows, anyone using these today would have to manually write a declaration for these functions, and they would not notice if boost had changed the code at compile-time, risking corruption at runtime.  
In other words, I think this can only affect really irresponsible developers.  
IMO for quality reasons, protecting users that do things correctly must take priority over compatibility with incorrect and obviously irresponsible use (if such even exists).  
I would however agree that if there is a use-case for those functions, it would be good to wrap them in C++ functions providing this functionality while also being part of the official API - though not sure if making this an official API is desirable.  
But, as so far anyone negatively impacted by this is purely hypothetical (as said, the example you provided would not have any issue but would in fact benefit), I am not sure it's really worth worrying about?

---

## Comment 5

> Username: olk  
> Created_at: 2024-10-08 07:37:26 UTC  
> Updated_at: 2024-10-08 07:37:40 UTC  
> Url: https://github.com/boostorg/context/pull/271#issuecomment-2399078990  

Unfortunately, tests fail for on MIPS and RISCV64:  
  
```  
  /usr/bin/ld: test_fiber.cpp:(.text+0x69c4): undefined reference to `jump_fcontext'  
  /usr/bin/ld: test_fiber.cpp:(.text+0x6a08): undefined reference to `ontop_fcontext'  
  /usr/bin/ld: test_fiber.cpp:(.text+0x6e20): undefined reference to `make_fcontext'  
```

---

## Comment 6

> Username: olk  
> Created_at: 2024-10-08 09:12:17 UTC  
> Url: https://github.com/boostorg/context/pull/271#issuecomment-2399299745  

> Unfortunately, tests fail for on MIPS and RISCV64:  
>   
> ```  
>   /usr/bin/ld: test_fiber.cpp:(.text+0x69c4): undefined reference to `jump_fcontext'  
>   /usr/bin/ld: test_fiber.cpp:(.text+0x6a08): undefined reference to `ontop_fcontext'  
>   /usr/bin/ld: test_fiber.cpp:(.text+0x6e20): undefined reference to `make_fcontext'  
> ```  
  
probably caused by misconfiguration of CI-tests

---

## Comment 7

> Username: olk  
> Created_at: 2024-10-08 09:12:33 UTC  
> Url: https://github.com/boostorg/context/pull/271#issuecomment-2399300346  

ty

---

## Comment 8

> Username: pdimov  
> Created_at: 2024-10-08 16:56:38 UTC  
> Url: https://github.com/boostorg/context/pull/271#issuecomment-2400381769  

This change seems to break Coroutine and Fiber on macOS.  
  
https://github.com/boostorg/boost/actions/runs/11232703851

---

## Comment 9

> Username: rdoeffinger  
> Created_at: 2024-10-08 19:37:28 UTC  
> Url: https://github.com/boostorg/context/pull/271#issuecomment-2400675492  

I am confused how it works anywhere actually?  
It does not work because fiber_fcontext.hpp is a header which calls make_fcontext etc.  
And while these are referred to as detail::make_fcontext, that is misleading. Due to extern "C" the symbol is the plain make_fcontext.  
One option is to revert this and instead change the name to something more descriptive like boost_context_make_fcontext.  
But in a C++ project like boost it seems a bit non-nice to export plain C symbols, without the type safety of name mangling etc.  
So a more involved option would be:  
- change the include/boost/context/detail/fcontext.hpp declaration to declare a real C++ function  
- define that function in e.g. src/fiber.cpp, also add the extern "C" make_fcontext declarations there and make the C++ make_fcontext call the C version  
  
I guess short-term reverting might be the way to go to not leave things broken in develop.

---

## Comment 10

> Username: rdoeffinger  
> Created_at: 2024-10-08 20:19:09 UTC  
> Url: https://github.com/boostorg/context/pull/271#issuecomment-2400746273  

This is clearly a hack misusing an existing cpp file, but this approach at least compiles on macOS:  
https://github.com/boostorg/context/commit/068bed6f12bdaf24b8bd9ead9557759c821f2cdc  
I can try to find time to polish it more if that seems a sensible way to go.  
(I confess I have not run the tests on it yet, because it's late and I forgot again how to run tests and could not find it quickly)

---

## Comment 11

> Username: rdoeffinger  
> Created_at: 2024-10-08 20:22:07 UTC  
> Url: https://github.com/boostorg/context/pull/271#issuecomment-2400751055  

Ran ./b2 libs/context/test and got only passes.  
Same for libs/coroutine/test and libs/fiber/test

---

## Comment 12

> Username: olk  
> Created_at: 2024-10-09 06:47:46 UTC  
> Url: https://github.com/boostorg/context/pull/271#issuecomment-2401464426  

> I can try to find time to polish it more if that seems a sensible way to go.  
  
That would be nice ...

---
