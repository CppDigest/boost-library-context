# #178 - Converting shared_ptr<const T> fails [Open]

> Username: mg262  
> Created at: 2017-11-29 13:33:34 UTC  
> Updated at: 2017-11-30 14:50:24 UTC  
> Url: https://github.com/boostorg/python/issues/178  

Boost Python (1.63) works well with `shared_ptr<T>`; if I write this in C++:  
  
    shared_ptr<Foo> create_shared_ptr() { return shared_ptr{...}; }  
    void accept_shared_ptr(const shared_ptr<Foo>& p) { }  
  
    ...  
  
    class_<Foo, boost::noncopyable>("Foo", no_init); //Expose abstract class  
  
    register_ptr_to_python< shared_ptr<Foo> >();  
    def("create_shared_ptr", create_shared_ptr);  
    def("accept_shared_ptr", accept_shared_ptr);  
  
Then I can write this in Python and everything works:  
  
    accept_shared_ptr(create_shared_ptr())  
  
**The problem comes when I try and wrap `shared_ptr<const Foo>`.** (Which I need to do because I am wrapping a library that returns this.) If I modify the C++ functions as follows:  
  
    shared_ptr<const Foo> create_shared_ptr() { return shared_ptr{...}; }  
    void accept_shared_ptr(const shared_ptr<const Foo>& p) { }  
  
Then I get the error:  
  
>     Boost.Python.ArgumentError: Python argument types in  
>         w3w_python.accept_shared_ptr(Foo)  
>     did not match C++ signature:  
>         accept_shared_ptr(std::shared_ptr<Foo const>)  
  
It seems the internals are implementing conversion from python `Foo` to C++ `shared_ptr<Foo>`, but not to C++ `shared_ptr<const Foo>`. Using  
  
    register_ptr_to_python< shared_ptr<const Foo> >();  
  
doesn't help. How can I fix this?

---

## Comment 1

> Username: mg262  
> Created at: 2017-11-29 18:09:31 UTC  
> Updated at: 2017-11-29 18:09:50 UTC  
> Url: https://github.com/boostorg/python/issues/178#issuecomment-347946599  

See https://stackoverflow.com/questions/47553964/using-boost-python-with-shared-ptrconst-t for a report that the issue does not arise on 1.62. I tried the code from that answer, and the issue does arise on 1.63.   
  
There is a commit `Add support for std::shared_ptr.` on Sep 23, 2016. (github.com/boostorg/python/commit/…). Boost 1.62 was released on Sep 1, and 1.63 on Nov 2. Is it possible that this commit broke something?  
  
NB I'm using g++ 6.4 and compiling with -std=c++11.

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2017-11-30 14:50:23 UTC  
> Url: https://github.com/boostorg/python/issues/178#issuecomment-348210093  

The commit (I assume you are referring to https://github.com/boostorg/python/commit/97e4b34a15978ca9d7c296da2de89b78bba4e0d5) introduces support for `std::shared_ptr<>`, so what you describe can't possible be a regression.  
Or are you observing a regression using `boost::shared_ptr<>` ? The commit above shouldn't affect that at all. Please provide a test case that I can use to reproduce the issue (and which makes clear whether we are talking about `std::shared_ptr` or `boost::shared_ptr`.  
Thanks,
