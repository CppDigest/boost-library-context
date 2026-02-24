# #485 - For each loops do seemingly unintended copies [Closed]

> Username: mitchellwong  
> Created at: 2017-06-13 17:00:46 UTC  
> Updated at: 2017-06-14 00:38:21 UTC  
> Closed at: 2017-06-14 00:38:21 UTC  
> Url: https://github.com/boostorg/beast/issues/485  

Beast version 55  
  
### Steps necessary to reproduce the problem  
  
The default CMake build  
  
### All relevant compiler information  
  
Compiling with clang 4.0 with the CXX flag -Wrange-loop-analysis indicates that several for each loops do a copy instead of getting elements by reference.  
  
For instance   
`/Users/mgwong/Beast/test/core/buffer_bench.cpp:69:25: warning: loop variable 'buffer' is always a copy because the range of type 'const beast::basic_multi_buffer<std::__1::allocator<char> >::mutable_buffers_type' does not return a reference [-Wrange-loop-analysis]  
        for(auto const& buffer : buffers)`  
  
This seems to be due to a number of `using reference = value_type;` instead of `using reference = value_type&;` throughout several classes.  
  
[beast-warnings.txt](https://github.com/vinniefalco/Beast/files/1072232/beast-warnings.txt)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-13 17:27:54 UTC  
> Updated at: 2017-06-13 17:28:41 UTC  
> Url: https://github.com/boostorg/beast/issues/485#issuecomment-308189768  

Its not a problem of the loops but rather, because the ranges returned by functions like `buffer_prefix` and `buffer_cat` return values instead of references. This is a problem in the C++ Standard, which is partially addressed by the Range v3 proposal. I am also attempting to address it in the Networking-TS:  
https://cplusplus.github.io/LWG/lwg-active.html#2779  
  
You could change the loop:  
```  
for(boost::asio::const_buffer const buffer : buffers)  
```  
  
If you would like to submit a pull request with this change, and it silences the warnings, I would strongly consider merging it. We can also add `-Wrange-loop-analysis` to the clang flags in CMakeLists.txt and Jamfile.

---

## Comment 2

> Username: mitchellwong  
> Created at: 2017-06-13 18:10:00 UTC  
> Url: https://github.com/boostorg/beast/issues/485#issuecomment-308201609  

Sorry, I'm a bit confused. ForwardIterator currently dereferences to a lvalue reference. Your proposal is to allow it to dereference to an rvalue. Could you please elaborate more on why `buffer_cat` can't return a reference?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-06-13 18:19:06 UTC  
> Updated at: 2017-06-13 18:19:49 UTC  
> Url: https://github.com/boostorg/beast/issues/485#issuecomment-308204044  

>ForwardIterator currently dereferences to a lvalue reference  
  
If that was the only problem, we could just "stash" a value type, inside the iterator itself, created on demand in `operator*` and return a reference to it. But this part of the specification prevents that:  
  
From http://en.cppreference.com/w/cpp/concept/ForwardIterator  
_"If a and b compare equal (a == b is contextually convertible to true) then either they are both non-dereferenceable or *a and *b are references bound to the same object"_  
  
That means that when dereferenced, two different iterators must point to the same location in memory. This rules out returning a member of the iterator, i.e. "stashing" a `value_type` in the iterator itself.  
  
`buffer_prefix_view` returns a modified copy of the value from the range it adapts:  
https://github.com/vinniefalco/Beast/blob/master/include/beast/core/impl/buffer_prefix.ipp#L92  
  
Even worse, `buffer_cat` is capable of wrapping a series of buffer sequences with different value types (they are converted to all `mutable_buffer` or `const_buffer`). How could we return a reference? Note that `iter<I>()` in the code below can return a different type for each value of `I`:  
https://github.com/vinniefalco/Beast/blob/master/include/beast/core/impl/buffer_cat.ipp#L252  
  
I tried all sorts of ways to make it a reference but I failed. If you would like to try, you have my support. But I don't think its possible.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-06-13 18:24:34 UTC  
> Updated at: 2017-06-13 18:47:00 UTC  
> Url: https://github.com/boostorg/beast/issues/485#issuecomment-308205529  

Upon further research, I think that modifying the range-for loops in the Beast implementation is warranted. Given the specifications of **ConstBufferSequence** and **MutableBufferSequence**, specifically that the iterators they provide are *convertible* to `const_buffer` and `mutable_buffer` respectively, the loops should be written as:  
```  
for(boost::asio::const_buffer buffer : buffers)  
    ...  
  
for(boost::asio::mutable_buffer buffer : buffers)  
    ...  
```  
  
There is precedent, Asio implementation does the same thing:  
https://github.com/boostorg/asio/blob/develop/include/boost/asio/buffer.hpp#L1412  
and  
https://github.com/boostorg/asio/blob/develop/include/boost/asio/buffer.hpp#L1415

---

## Comment 5

> Username: mitchellwong  
> Created at: 2017-06-13 21:45:40 UTC  
> Url: https://github.com/boostorg/beast/issues/485#issuecomment-308258085  

Thanks for the explanation.  
  
After experimenting, I found that due to templates, most of the occurences of this warning can't easily be corrected because `-Wrange-loop-analysis` also warns if you copy unnecessarily. Sometimes using references doesn't do a copy and sometimes it does, so I think the preferred solution in this case is to just ignore the warning.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-06-13 21:50:31 UTC  
> Updated at: 2017-06-13 21:50:39 UTC  
> Url: https://github.com/boostorg/beast/issues/485#issuecomment-308259249  

It should be fixed in **v56**, I don't see any warnings (and they are turned on now): https://github.com/vinniefalco/Beast/commit/f8fb4d9036efca300086ac8e6ac295c89386dc3f
