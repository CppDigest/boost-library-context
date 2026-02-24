# #32 Clean up function_input_iterator [Merged]

> Username: morinmorin  
> Created at: 2017-09-06 16:03:41 UTC  
> Updated at: 2017-09-07 16:15:48 UTC  
> Merged at: 2017-09-07 15:46:07 UTC  
> Closed at: 2017-09-07 15:46:07 UTC  
> Url: https://github.com/boostorg/iterator/pull/32  

This PR makes a few small-scale clean ups  
- Use `boost::addressof` instead of `&`.  
- Remove unnecessary headers in test.  
- Replace `BOOST_DEDUCED_TYPENAME` with `typename`. (`function_input_iterator.hpp` includes headers that do not support ancient compilers. So it doesn't make sense to continue to use `BOOST_DEDUCED_TYPENAME`.)  
  
and one moderate-scale clean up  
- Remove dead class template (`function_reference_input_iterator`).  
  
I have a plan to make another larger-size clean up:  
integrate `function_pointer_input_iterator` into `function_input_iterator`. These two classes have many duplicate codes, which is bad for maintenance.

---

## Comment 1

> Username: Lastique  
> Created_at: 2017-09-06 18:55:50 UTC  
> Url: https://github.com/boostorg/iterator/pull/32#issuecomment-327580580  

Are you sure `function_reference_input_iterator` is actually unused? What if `make_function_input_iterator` is called with a reference to a function? I suspect, this might have been a workaround for some compiler. We may not need a separate iterator class, but we probably want to make sure this case is handled by `function_pointer_input_iterator`.

---

## Comment 2

> Username: morinmorin  
> Created_at: 2017-09-07 11:27:38 UTC  
> Url: https://github.com/boostorg/iterator/pull/32#issuecomment-327772261  

> Are you sure `function_reference_input_iterator` is actually unused? What if `make_function_input_iterator` is called with a reference to a function?   
  
I'm sure that `make_function_input_iterator` does not instantiate `function_reference_input_iterator`. Both `make_function_input_iterator(func)` and `make_function_input_iterator(&func)` (`func` is the name of a nullary function) use this overload:  
```  
template <class Function, class Input>  
inline function_input_iterator<Function*, Input>  
make_function_input_iterator(Function * f, Input state)  
{  
    return function_input_iterator<Function*, Input>(f, state);  
}  
```  
which instantiates `function_input_iterator` with `Function*` (`Function` is the function type of `func`). Then, this instantiates `function_pointer_input_iterator` with `Function*`.   
  
> I suspect, this might have been a workaround for some compiler.  
  
I don't think that's the case. Besides template argument deduction bugs, such compilers allow **pointers to references** (which is not allowed by the C++ standard) — too wildly broken.  
  
`function_reference_input_iterator` is instantiated only when a function reference is passed as a template argument to `function_input_iterator`. This results in compiler errors, since this attempts to instantiate  (as a base class) `function_input_iterator` with a pointer to a reference.

---

## Comment 3

> Username: Lastique  
> Created_at: 2017-09-07 15:11:56 UTC  
> Url: https://github.com/boostorg/iterator/pull/32#issuecomment-327830155  

> `function_reference_input_iterator` is instantiated only when a function reference is passed as a template argument to `function_input_iterator`.  
  
Is this use case no longer supported after this change?

---

## Comment 4

> Username: morinmorin  
> Created_at: 2017-09-07 15:27:25 UTC  
> Url: https://github.com/boostorg/iterator/pull/32#issuecomment-327834926  

What use case?  
  
- Both `make_function_input_iterator(func)` and `make_function_input_iterator(&func)` (`func` is a nullary function) works fine before and after the change.   
- `function_input_iterator<Function&>` (`Function` is a nullary function type) fails to compile before and after the change:   
  - Before change: `function_input_iterator<Function&>` -> `function_reference_input_iterator<Function&>` -> Error (due to attempting to use a pointer to a function).  
  - After change: `function_input_iterator<Function&>` -> Error (due to attempting to use a pointer to a function).

---

## Comment 5

> Username: Lastique  
> Created_at: 2017-09-07 15:35:22 UTC  
> Url: https://github.com/boostorg/iterator/pull/32#issuecomment-327837363  

> What use case?  
  
The second one. I thought `function_input_iterator<Function&>` used to work, or at least was supposed to work while the change makes it intentionally unsupported.

---

## Comment 6

> Username: morinmorin  
> Created_at: 2017-09-07 15:43:20 UTC  
> Url: https://github.com/boostorg/iterator/pull/32#issuecomment-327839692  

From the comment in the testcase  
```  
// test the iterator with a reference to a function  
vector<int>().swap(generated);  
copy(  
    boost::make_function_input_iterator(ones_function, 0),  
    boost::make_function_input_iterator(ones_function, 10),  
    back_inserter(generated)  
);  
```  
, I thought that `function_reference_input_iterator` is implemented to support `boost::make_function_input_iterator(func)`. However, this case is covered by `function_pointer_input_iterator`. And `function_reference_input_iterator` has been broken from the beginning. So I think it's OK to unsupport `function_reference_input_iterator`.

---

## Comment 7

> Username: morinmorin  
> Created_at: 2017-09-07 16:00:23 UTC  
> Url: https://github.com/boostorg/iterator/pull/32#issuecomment-327844812  

Thanks for merging and for your time to review this, Andrey.  
  
If I change `typename result_of<Function ()>::type` in `function_input_iterator` to  
```  
typename result_of<  
    typename mpl::if_<function_types::is_function<Function>, Function&, Function>::type ()  
>::type  
```  
then we can support `function_input_iterator<Function>` (which is not supported by now).   
  
With this change, `function_input_iterator` will support  
  
- `CallableClass` (neither `*` nor `&`),  
- `Function`,  
- `Function*`.  
  
This set is the same as that of `reference_wrapper`. So I think it makes some sense to support `function_input_iterator<Function>` and drop `function_input_iterator<Function&>`.

---

## Comment 8

> Username: Lastique  
> Created_at: 2017-09-07 16:15:48 UTC  
> Url: https://github.com/boostorg/iterator/pull/32#issuecomment-327849240  

From the docs:  
  
> The class Function Input Iterator class takes two template parameters Function and State. These two template parameters tell the Function Input Iterator the type of the function to encapsulate and the type of the internal state value to hold.  
  
So it looks like `Function` is supposed to be the type to encapsulate, which cannot be a function type (i.e. not a pointer or a reference to function). I suppose we could add support for `Function` being a function type also, but then we might allow it to be a reference to a function as well. As long as all those flavors are served by `function_pointer_input_iterator` it should be fine.

---
