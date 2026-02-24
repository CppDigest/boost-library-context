# #428 Custom PNG error handler [Closed]

> Username: simmplecoder  
> Created at: 2020-01-22 17:24:40 UTC  
> Updated at: 2020-08-01 20:08:17 UTC  
> Closed at: 2020-02-09 17:16:28 UTC  
> Url: https://github.com/boostorg/gil/pull/428  

### Description  
  
This pull request sets custom error handler function to avoid all of the problems stated in the referenced pages, and also hopefully deals with the resource leak that happens when objects with non-trivial destructors are declared after `setjmp` part.  
  
### References  
  
http://www.libpng.org/pub/png/book/chapter13.html  
  
http://www.libpng.org/pub/png/book/chapter14.html  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [ ] Find some way to bypass C ABI  
- <s>Add test case(s)</s> not applicable  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: simmplecoder  
> Created_at: 2020-01-22 17:25:35 UTC  
> Url: https://github.com/boostorg/gil/pull/428#issuecomment-577295423  

Sorry for being stubborn about this at the start. Since hunting for stray `setjmp` sections is quite hard, setting custom error handler seems to be the easiest option, and avoids quite a lot of UB as well.

---

## Comment 2

> Username: misos1  
> Created_at: 2020-01-22 19:29:27 UTC  
> Url: https://github.com/boostorg/gil/pull/428#issuecomment-577347090  

I think for this we need to be sure that throwing C++ exceptions from callbacks called from C compiled library is really ok https://github.com/boostorg/gil/pull/414#issuecomment-573877218. I think it should be.

---

## Comment 3

> Username: misos1  
> Created_at: 2020-01-22 20:14:44 UTC  
> Url: https://github.com/boostorg/gil/pull/428#issuecomment-577365637  

https://gcc.gnu.org/onlinedocs/gcc/Code-Gen-Options.html:  
  
> -fexceptions  
> Enable exception handling. Generates extra code needed to propagate exceptions. For some targets, this implies GCC generates frame unwind information for all functions, which can produce significant data size overhead, although it does not affect execution. If you do not specify this option, GCC enables it by default for languages like C++ that normally require exception handling, and disables it for languages like C that do not normally require it. However, you may need to enable this option when compiling C code that needs to interoperate properly with exception handlers written in C++. You may also wish to disable this option if you are compiling older C++ programs that don’t use exception handling.

---

## Comment 4

> Username: simmplecoder  
> Created_at: 2020-01-24 10:18:18 UTC  
> Url: https://github.com/boostorg/gil/pull/428#issuecomment-578070814  

@misos1 , you're right, at least in arch pacman repository, the libpng version is not compiled with exceptions enabled. Could you please run `objdump -TC /usr/lib/libpng.so | grep unwind` on your machine if it has ubuntu installed?  
  
I believe I will still keep this PR open, just assign a tag that this is still work in progress. I believe there should be some way to bypass the restrictions.

---

## Comment 5

> Username: misos1  
> Created_at: 2020-01-24 16:16:51 UTC  
> Url: https://github.com/boostorg/gil/pull/428#issuecomment-578197258  

@simmplecoder Output was empty. I also tested to create C shared object with function which will call function passed as argument and C++ program which will call this function in try block and pass function which throws exception. It worked without -fexceptions. I tried to compile it also with -fexceptions and resulting .so file was exactly same as without it and was same also when I tried with -fno-exceptions. Your objdump command result was empty for all generated .so files.

---

## Comment 6

> Username: misos1  
> Created_at: 2020-01-24 16:36:28 UTC  
> Url: https://github.com/boostorg/gil/pull/428#issuecomment-578205031  

@simmplecoder So it probably depends on architecture as they wrote "...you may need to enable this option...". At least on ubuntu 18.04 on x64 this should work just fine but I read somewhere that someone had problem with this and -fexceptions helped (maybe windows?).

---

## Comment 7

> Username: simmplecoder  
> Created_at: 2020-01-24 17:38:23 UTC  
> Updated_at: 2020-01-24 17:52:55 UTC  
> Url: https://github.com/boostorg/gil/pull/428#issuecomment-578228052  

@misos1 , it might be due to this: https://stackoverflow.com/questions/5107948/throwing-c-exceptions-across-dll-boundaries . Either way, two out of two people on #standardese channel on slack told me that throwing exceptions across C code will not work reliably until unwind tables were generated prior (e.g. `-fexceptions` flag).   
  
My next suggestion is this: allocate memory for all currently local variables, and construct in place. On each allocation, increase the object counter. Then handroll the unwind table:  
  
    /*cast into right type through `reinterpret_cast`*/  
    switch (error_object) {  
        case N:  
            error_object->/*Nth member*/->~type();  
        case 3:  
            error_object->/*3rd member*/->~type();  
        case 2:  
            error_object->/*2nd member*/->~type();  
        case 1:  
            error_object->/*1st member*/->~type();  
    }  
  
    std::longjmp(error_obj->the_jmp_buf);  
  
The thing is that libpng allows to set a custom error object that will be passed along when an error is encountered, and said object can be retrieved. If all local variables are constructed into said error object, we can track which ones are constructed with just one `setjmp` at the top. Preferably the error object holds the memory in `std::unique_ptr`, overloads the `operator ->`, and is declared as first variable in a series of png calls before `setjmp`. A sketch of such a type would be like this:  
  
    template <typename UnderlyingStruct>  
    class png_error_object  
    {  
        std::unique_ptr<void*> object;  
        std::size_t object_counter = 0;  
    public:  
        // non-movable and non-copyable  
        UnderlyingStruct* operator->()  
        {  
            return reinterpret_cast<UnderlyingStruct*>(object.get());  
        }  
  
        template <typename T, typename ... Args>  
        void construct(std::size_t offset, Args&& ... args)  
        {  
            auto location = reinterpret_cast<unsigned char>(object.get()) + offset;  
            new (location) T(std::forward<Args>(args)...);  
           ++object_counter;  
        }  
    };  
  
**TL;DR** My idea is even worse footshooter than before. Anyway, out of ideas for now.  
  
<S>But this is only exchanging one problem for another. It will be very brittle and forgetting to add 1 will still leak resources. I guess we can pick our poison.</S>

---

## Comment 8

> Username: misos1  
> Created_at: 2020-01-24 21:27:33 UTC  
> Updated_at: 2020-01-24 21:33:07 UTC  
> Url: https://github.com/boostorg/gil/pull/428#issuecomment-578308718  

@simmplecoder Regarding suggestion about allocations would not be this little overkill? I think it is simpler to just avoid declaring automatic variables with non-trivial destructors between setjmp and last thing which can longjmp into this setjmp. It is ok to declare empty vector before setjmp and call resize after setjmp and it will be destroyed properly. And maybe would be good to note something in comments about this or somehow mark such dangerous parts of code.

---

## Comment 9

> Username: simmplecoder  
> Created_at: 2020-01-31 06:03:25 UTC  
> Url: https://github.com/boostorg/gil/pull/428#issuecomment-580595574  

@misos1 , I believe we could use some sort of `std::optional`, and just declare everything at the top. Then we'll only need one place to jump to. The destructor should properly release resources. I would like to avoid branching and more manual labor, as we might be playing catch up with the bug reports as new features arrive. Perhaps I'm being too pedantic and wanting one solution to do it all. The solution with `std::optional` would also solve the problem in libjpeg, or any similar ones.  
  
@mloskot, @stefanseefeld would it be possible and feasible to rely on `boost::optional` for this?

---

## Comment 10

> Username: misos1  
> Created_at: 2020-01-31 16:18:24 UTC  
> Url: https://github.com/boostorg/gil/pull/428#issuecomment-580800350  

@simmplecoder I think `optional` would have more sense when it is expected that at some point is undecided whether it has or does not have value. When is for example `optional<vector>` always initialised at some point and after used we always know that it is initialised. If it is not used before that point but only declared then I would simply use default-initialised `vector`.

---

## Comment 11

> Username: stefanseefeld  
> Created_at: 2020-01-31 16:24:55 UTC  
> Url: https://github.com/boostorg/gil/pull/428#issuecomment-580803533  

I agree. Also, dragging in a new external dependency (to `boost::optional`) should not be done light-heartedly.

---

## Comment 12

> Username: mloskot  
> Created_at: 2020-01-31 17:50:50 UTC  
> Updated_at: 2020-01-31 17:52:09 UTC  
> Url: https://github.com/boostorg/gil/pull/428#issuecomment-580838579  

I agree. I'd rather prefer to see GIL switch over to C++17 than drag `boost::optional`.  
  
Copying from [#boost-gil channel](https://cpplang.slack.com/archives/CSVT0STV2/p1580474583003900) today:  
  
> I'd say, good enough is a good solution. I think that if:  
> - Solution **does not introduce any regressions or any new issues**  
> - Solution **improves current state** (even if improvement is minor)  
> Then it is a good enough solution, at least for now until we work out a better one in next iterations in future.  
  
[Doing it ~well~ okey now is much better than doing it perfectly later](https://seths.blog/2005/03/dont_shave_that/) 😄

---

## Comment 13

> Username: mloskot  
> Created_at: 2020-02-06 19:21:13 UTC  
> Updated_at: 2020-02-06 19:25:58 UTC  
> Url: https://github.com/boostorg/gil/pull/428#issuecomment-583067070  

@simmplecoder To sum it up, I think we should put this one on back-burner for now.  
  
Simpler to tackle is #416 which I think we've come to agreement on fairly basic changes to the I/O code as showed in https://github.com/boostorg/gil/issues/416#issuecomment-573899376

---

## Comment 14

> Username: simmplecoder  
> Created_at: 2020-02-09 17:17:53 UTC  
> Url: https://github.com/boostorg/gil/pull/428#issuecomment-583870005  

@mloskot I'll keep it closed for now then, and switch to #416 .  
  
If anybody would like to continue working on this, please let me know on slack. I will provide you with all the knowledge I gathered about the issue.

---

## Comment 15

> Username: mloskot  
> Created_at: 2020-02-09 17:45:24 UTC  
> Url: https://github.com/boostorg/gil/pull/428#issuecomment-583872774  

@simmplecoder Okey. Thanks for all the work on the `setjmp` issue

---
