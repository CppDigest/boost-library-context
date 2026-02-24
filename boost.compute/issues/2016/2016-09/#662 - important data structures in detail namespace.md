# #662 - important data structures in detail namespace [Open]

> Username: Ulfgard  
> Created at: 2016-09-27 12:24:02 UTC  
> Updated at: 2017-04-24 16:40:52 UTC  
> Url: https://github.com/boostorg/compute/issues/662  

Hi,  
  
I am trying to extend the boost.compute mechanisms to my matrix library and run into trouble implementing certain functionality as the mechanisms by which compute generates kernels out of expressions are hidden in detail.  
  
This is most notably the boost::compute::detail::meta_kernel which is required for example when i want to assign a matrix expression e.g. A=exp(M) <=> A_ij = exp(M_ij). I need access to this one for a) generating a kernel from a functor-expression and b) to extend the provided functions using new functionality  
  
Another useful feature hidden in detail is boost::compute::detail::unpack in compute/functional/detail/unpack.hpp as used for implementing boost::compute::inner_prod. I use this one to implement plus-assignment of vectors using v+=b <=>  v_i = v_i + b_i. i.e. I take the implementation of inner_prod and replace accumulate by transform.  
  
while it is possible to be on the safe side for unpack and just copy& paste it into my own implementation, this option does not exist on meta_kernel. So I am stuck with: either use it and feel the pain if the detail namespace changes, or hit a road block.  
  
A possible way around the need of unpack would be if transform_iterator would have the necessary type magic to check whether its function takes 1 or more arguments and use unpack in that case.  
##

---

## Comment 1

> Username: jszuppe  
> Created at: 2016-09-27 12:50:27 UTC  
> Url: https://github.com/boostorg/compute/issues/662#issuecomment-249855125  

Hmm.. Operation  `v+=b <=> v_i = v_i + b_i` is /should be trivial to implement using Boost.Compute, just use a transform algorithm which takes two input vectors and pass a binary function object of type `plus<vector_type>`, or use lambda expression like this: `_1 + _2`. The lambda expression approach should also work with `transform_iterator` which has a `zip_iterator` as an input iterator type, however,  you'll have to use `lamnda::get<>()`. There are some examples in tests.

---

## Comment 2

> Username: jszuppe  
> Created at: 2016-09-27 12:53:59 UTC  
> Url: https://github.com/boostorg/compute/issues/662#issuecomment-249855923  

I don't understand what is the issue with `meta_kernel`. It's possible to write a custom function which can be passed to various algorithms or even iterators.

---

## Comment 3

> Username: Ulfgard  
> Created at: 2016-09-27 13:14:02 UTC  
> Url: https://github.com/boostorg/compute/issues/662#issuecomment-249860924  

Hi,  
  
the issue with meta_kernel is that it is in namespace detail, which by boost convention is recognized as "implementation detail, subject to change, do not touch". As i got burned pretty bad in the past by other boost libraries, I stay away from it. I would rather not having a boost 1.6x break my whole library because something in detail changed :)  
  
I agree that it is possible to implement it using lambdas, and as I said i could just use the implementation given, especially as it leads to such beautiful code:  
  
```  
make_transform_iterator(  
    make_zip_iterator(boost::make_tuple(first1, first2) ),  
    detail::unpack(multiplies<input_type>())//pure beauty!  
)  
```  
  
But again, as it is in detail I wonder whether the function was just not deemed as important or whether there are some hidden problems with it. I would certainly like to see it in the public namespace.

---

## Comment 4

> Username: jszuppe  
> Created at: 2016-09-27 13:33:16 UTC  
> Url: https://github.com/boostorg/compute/issues/662#issuecomment-249865853  

Ok, so you just want to be able to do this using Boost.Compute functions, not only lambda expressions ;-) Ok. I assume that Kyle had some reasons for hiding the `unpack`. Maybe it's not prepared for all functions user may want to unpack, I'm not sure. We have to wait for him to comment. I'll look into all concerns you've posted here tomorrow, I'm not home now.

---

## Comment 5

> Username: jszuppe  
> Created at: 2016-09-27 13:47:28 UTC  
> Url: https://github.com/boostorg/compute/issues/662#issuecomment-249869695  

As for the `meta_kernel`, it's created for internal use and it works well there, but, I think, it's not considered good enough yet to be in the main namespace. However, personally I don't think that it'll change much, even when it's promoted out of the `detail::`. Currently, I dont have plans for working on this.
