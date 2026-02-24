# #45 - make_optional broken with optional references [Closed]

> Username: merryhime  
> Created at: 2017-11-28 19:29:36 UTC  
> Updated at: 2017-11-28 20:54:32 UTC  
> Closed at: 2017-11-28 20:54:32 UTC  
> Url: https://github.com/boostorg/optional/issues/45  

c695be1 breaks this use-case.  
  
Please see below for a minimal testcase.  
  
    #include <boost/optional.hpp>  
      
    struct Foo {};  
  
    Foo f;  
  
    Foo create_a_foo() {  
        return {};  
    }  
  
    boost::optional<Foo&> frob() {  
        return boost::make_optional<Foo&>(create_a_foo());  
    }  
  
    int main(){}

---

## Comment 1

> Username: akrzemi1  
> Created at: 2017-11-28 20:08:59 UTC  
> Url: https://github.com/boostorg/optional/issues/45#issuecomment-347648138  

What do you expect of this test case? That it should compile?

---

## Comment 2

> Username: merryhime  
> Created at: 2017-11-28 20:12:59 UTC  
> Url: https://github.com/boostorg/optional/issues/45#issuecomment-347649224  

Yes.

---

## Comment 3

> Username: akrzemi1  
> Created at: 2017-11-28 20:25:10 UTC  
> Url: https://github.com/boostorg/optional/issues/45#issuecomment-347652492  

But you are creating a temporary `Foo` inside function `forb` if you tie it to a reference, and return this reference outside function `forb` the temporary object will by then have been destroyed, and your reference is dangling, which will cause UB as soon as you will try to dereference it. I think it is a good safety feature that this bug is detected and reported at compile-time.

---

## Comment 4

> Username: merryhime  
> Created at: 2017-11-28 20:37:20 UTC  
> Updated at: 2017-11-28 20:42:57 UTC  
> Url: https://github.com/boostorg/optional/issues/45#issuecomment-347655678  

I do apologise, I wasn't thinking properly regarding that test-case. See below for two better examples of something that doesn't currently compile.  
  
    #include <array>  
    #include <cstddef>  
    #include <boost/optional.hpp>  
  
    struct Foo {};  
  
    boost::optional<const Foo&> frob(std::size_t i) {  
        static const std::array<Foo, 3> a = {};  
        return boost::make_optional<const Foo&>(a[i]);  
    }  
      
    Foo f;  
      
    boost::optional<const Foo&> frob2() {  
        return boost::make_optional<const Foo&>(f);  
    }

---

## Comment 5

> Username: akrzemi1  
> Created at: 2017-11-28 20:53:13 UTC  
> Url: https://github.com/boostorg/optional/issues/45#issuecomment-347659922  

Ok, but are you not using `make_optional` like that? The goal of using it is to deduce the `T`. If you are specifying it anyway, why not just type:  
  
```c++  
boost::optional<const Foo&> frob(std::size_t i) {  
    static const std::array<Foo, 3> a = {{}, {}, {}};  
    return boost::optional<const Foo&>(a[i]);  
}  
```  
? (which is shorter)

---

## Comment 6

> Username: merryhime  
> Created at: 2017-11-28 20:54:32 UTC  
> Url: https://github.com/boostorg/optional/issues/45#issuecomment-347660308  

That's a fair point. Thanks for your time.
