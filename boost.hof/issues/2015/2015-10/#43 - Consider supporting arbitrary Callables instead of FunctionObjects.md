# #43 - Consider supporting arbitrary Callables instead of FunctionObjects [Closed]

> Username: ldionne  
> Created at: 2015-10-04 01:55:33 UTC  
> Updated at: 2015-12-17 20:29:51 UTC  
> Closed at: 2015-12-17 20:29:51 UTC  
> Url: https://github.com/boostorg/hof/issues/43  

Most adaptors require `FunctionObject`s. Is there any reason not to allow arbitrary `Callable`s, and to use something like `std::invoke` to call the functions instead? That would be slightly more flexible, as one could use member pointers & al instead of regular function objects. However, there's probably a non-negligible compile-time penalty if this is supported everywhere in the library.

---

## Comment 1

> Username: pfultz2  
> Created at: 2015-10-05 04:56:03 UTC  
> Url: https://github.com/boostorg/hof/issues/43#issuecomment-145430290  

It would increase the complexity and probably the compile-time. There would not just be the extra compile-time from calling something like `std::invoke`, but also all the specializations needed to store the function pointers. Plus it would triple all the overloads.  
  
For 90% of the time, the user is using either a function object or lambda. For the other 10%, it is simple enough to just used `std::mem_fn` or `fit::mutable_`.

---

## Comment 2

> Username: pfultz2  
> Created at: 2015-10-25 22:12:07 UTC  
> Url: https://github.com/boostorg/hof/issues/43#issuecomment-150979549  

Well supporting the general "Callables" wouldn't add too much compile-time for function objects, since it would just be check for a class in the current case(which is already done). However, supporting mutable and rvalue function calls would be more problematic.   
  
I would prefer to support "Callable" only for `const` overloads still. Besides the extra compile-time for the overloads, mutable overloads are too problematic. That is because most implementations copy the function objects, which can affect the internal mutable state. It is better to use `std::ref` instead. And the library provides the `mutable_` adaptor for when it becomes absolutely necessary.  
  
Hopefully, that sounds reasonable.

---

## Comment 3

> Username: ldionne  
> Created at: 2015-10-29 15:08:44 UTC  
> Updated at: 2015-10-29 15:09:41 UTC  
> Url: https://github.com/boostorg/hof/issues/43#issuecomment-152208910  

Paul, I don't understand why `std::invoke` makes things more complex w.r.t. non-`const` overloads, or even why you have to store function objects in some way. The way I see it, it's only a matter of calling `std::invoke` as defined [on cppreference](http://en.cppreference.com/w/cpp/utility/functional/invoke) instead of calling the functions as `f(...)` inside Fit's code. Also, the compile-time penalty I was referring to was simply the overload resolution for the `std::INVOKE` function.

---

## Comment 4

> Username: pfultz2  
> Created at: 2015-10-29 18:28:58 UTC  
> Url: https://github.com/boostorg/hof/issues/43#issuecomment-152276433  

> Paul, I don't understand why std::invoke makes things more complex w.r.t. non-const overloads  
  
Right now, Fit only has `const` overloads. Besides a simpler implementation, mutable overloads can be problematic for the user(not the library). So I like the idea of the user going through an extra step of using `std::ref` or the `mutable_` adaptor when doing something problematic. Especially, since `std::ref` will more than likely give the user the intended behaviour, whereas mutable overloads wouldn't. However, I don't know if this is acceptable to a larger community though.  
  
> even why you have to store function objects in some way.  
  
Most adaptors inherit the functions, which allow for EBO(which is also important for `FIT_STATIC_LAMBDA_FUNCTION`). So if the function is not a class(such as member function pointer) then I will need an alternative way to store the function. So instead of writing this:  
  
``` cpp  
template<class F>  
struct protect_adaptor : F  
{  
    ...  
};  
```  
  
I could write something like this:  
  
``` cpp  
template<class F>  
using wrapper = typename std::conditional<(std::is_class<F>()), F, invoke_wrapper<F>>::type  
  
template<class F>  
struct protect_adaptor : wrapper<F>  
{  
    ...  
};  
```  
  
So the `invoke_wrapper` can store the object as a member and call `INVOKE`. So the extra penalty of overload resolution would only apply to non-classes. Although, everybody would incur the penalty of instantiating `std::conditional` and `std::is_class`, I think that is unavoidable.

---

## Comment 5

> Username: pfultz2  
> Created at: 2015-12-17 20:29:51 UTC  
> Url: https://github.com/boostorg/hof/issues/43#issuecomment-165573947  

Ok, Fit now supports `Callable` for most adaptors with one slight modification. The object is always invoked through `const`. This only affects function classes, and it doesn't change the current behaviour. I would prefer not to support non-const overloads for function objects, since they are problematic, and instead have the user use `std::ref` or `fit::mutable_` to achieve the desired behaviour. So I am closing this for now.
