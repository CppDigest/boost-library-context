# #306 - Issue with for_each and lvalues/rvalues [Closed]

> Username: ldionne  
> Created at: 2016-11-02 00:33:29 UTC  
> Updated at: 2018-01-01 20:15:27 UTC  
> Closed at: 2018-01-01 20:15:19 UTC  
> Url: https://github.com/boostorg/hana/issues/306  

Initially sent by email by @tzlaine.  
  
I'm trying to pretty-print a tree-like structure of Hana tuples.  An example of one is:  
  
```c++  
expression<  
    expr_kind::plus,  
    term<double> &,  
    term<int &&>  
>  
```  
  
When I print this, I get:  
  
```c++  
expr<+>  
    term<double> &[=1]  
    term<int &&> &[=42]  
```  
  
Note the extra `&` after the second term. This seems to be because I wrote the Hana tuple printer like this:  
  
```c++  
hana::for_each(expr.elements, [&os, indent, indent_str](auto element) {  
    print_impl(  
        os,  
        element, // <- always an lvalue, even if I try to print its reference type in this lambda  
        indent + 1,  
        indent_str  
    );  
});  
```  
  
I'd like to write it using some future `hana::for_each_with_type()` like this:  
  
```c++  
hana::for_each(expr.elements, [&os, indent, indent_str](auto element, auto type) {  
    // "type" is hana::basic_type<T>, where T is what "element" is stored as in expr.elements;  
    // now I can print out the lvalue/rvalue ref on the type if there is one.  
    print_impl(  
        os,  
        element,  
        indent + 1,  
        indent_str  
    );  
});  
```  
  
Is this necessary, or is there an easier way to do this?  
  
If there's no already a built-in way to do this, I'd settle for a `to_tuple_t(expr.elements)` that would give me the types, and I'd iterate over the `zip()` of the tuple and its types-tuple.  
  
For now, I'm writing a custom `for_each()` based on the index.

---

## Comment 1

> Username: ldionne  
> Created at: 2016-11-02 00:34:50 UTC  
> Url: https://github.com/boostorg/hana/issues/306#issuecomment-257740274  

@tzlaine Can you please show me how you define the `expression` template please? I think you might just be able to get away with something like  
  
``` c++  
hana::for_each(expr.elements, [&os, indent, indent_str](auto&& element) {  
    print_impl(  
        os,  
        std::forward<decltype(element)>(element),  
        indent + 1,  
        indent_str  
    );  
});  
```

---

## Comment 2

> Username: tzlaine  
> Created at: 2016-11-02 14:32:06 UTC  
> Updated at: 2016-11-02 14:32:52 UTC  
> Url: https://github.com/boostorg/hana/issues/306#issuecomment-257881833  

Sure; It's very simple:  
  
```  
template <expr_kind Kind, typename ...T>  
struct expression  
{  
    using this_type = expression<Kind, T...>;  
    using tuple_type = hana::tuple<T...>;  
  
    static const expr_kind kind = Kind;  
  
    tuple_type elements;  
};  
```

---

## Comment 3

> Username: badair  
> Created at: 2017-02-11 06:43:21 UTC  
> Updated at: 2017-02-11 06:44:47 UTC  
> Url: https://github.com/boostorg/hana/issues/306#issuecomment-279125862  

@tzlaine Essentially, you would like to be able to differentiate whether an original tuple template argument type was a reference type, which is impossible to tell once the tuple element instance is passed to a function? Do I understand this correctly? I do think this would be a useful library feature, even though you have probably rolled your own solution by now.  
  
Instead of offering a new `for_each_with_type` feature, we could "overload" `for_each` to support callables that accept either one or two parameters, and pass the appropriate `basic_type` whenever possible.

---

## Comment 4

> Username: ldionne  
> Created at: 2017-02-12 06:52:40 UTC  
> Url: https://github.com/boostorg/hana/issues/306#issuecomment-279200779  

As we've talked about in Issaquah, the problem here is that you're using references in the sequence, and that's not supported by Hana strictly speaking. Instead, I would suggest using a `std::reference_wrapper` and overloading your `print` on that. The problem with references is that they give rise to a lot of behavior that differs from object types, like this one, and so they're very difficult to support properly in the library.

---

## Comment 5

> Username: tzlaine  
> Created at: 2017-02-13 15:51:24 UTC  
> Url: https://github.com/boostorg/hana/issues/306#issuecomment-279431911  

@badair That's right, on both counts.  That's the problem I had, and I've already rolled a separate solution that works.  
  
@ldionne Also true -- I could do that.  I have not so far because, except for the problem outlined in this thread, I never ran into any major limitations using ref types in Hana tuples.

---

## Comment 6

> Username: ldionne  
> Created at: 2018-01-01 20:15:19 UTC  
> Url: https://github.com/boostorg/hana/issues/306#issuecomment-354673846  

Going to close as "wontfix", since the problem arises due to the use of references in a Hana container, which is not supported.
