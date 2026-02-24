# #665 - No sane way to create kernel with vector::operator[]/problem with buffer_iterator [Closed]

> Username: Ulfgard  
> Created at: 2016-10-05 06:21:19 UTC  
> Updated at: 2016-11-09 17:44:28 UTC  
> Closed at: 2016-11-09 09:12:40 UTC  
> Url: https://github.com/boostorg/compute/issues/665  

Hi,  
  
there is no sane way to create a kernel using the vectors operator[] or similar. The problem is that vector::operator[] only takes size_type and no expressions, the same with *(begin()+i). surprsingly, begin()[i] kind-of works because it takes arbitrary expressions and gives a different return type, which is against the intuition that *(begin()+i) and begin()[i] are equivalent. Now the following will silently fail:  
  
```  
template<class E>  
auto operator[](Econst& e) ->decltype(vector.begin()[e]){  
    return vector.begin()[e];  
}  
```  
  
begin() returns a buffer_iterator<T> which takes a buffer object by value. buffer_iterator<T>::operator[] then returns an object which take said buffer by const-reference. Thus when the original iterator goes out of scope, the returned reference object becomes invalid, even though the vector itself is still valid. This will manifest earliest in kernel invocation because the buffer object is in the best case invalid orin the worst case fails with a segfault.  
  
This goes broadly against the intuition that lifetime of references returned by an iterator are coupled to the lifetime of the underlying container.  
##

---

## Comment 1

> Username: jszuppe  
> Created at: 2016-10-05 08:45:08 UTC  
> Url: https://github.com/boostorg/compute/issues/665#issuecomment-251617040  

>  create a kernel using the vectors operator[] or similar.  
  
I don't get it. How do you want to use vector to create an OpenCL kernel? Would you like to use it in `meta_kernel`. If so, you should use iterators.  
  
> The problem is that vector::operator[] only takes size_type and no expressions, the same with *(begin()+i). surprsingly, begin()[i] kind-of works because it takes arbitrary expressions and gives a different return type, which is against the intuition that *(begin()+i) and begin()[i] are equivalent.  
  
`buffer_iterator::operator[]` is for internal usage only. To be precise, it's needed in `meta_kernel` to generate index expression for its buffer object.  
  
> begin() returns a buffer_iterator which takes a buffer object by value. buffer_iterator::operator[] then returns an object which take said buffer by const-reference. Thus when the original iterator goes out of scope, the returned reference object becomes invalid, even though the vector itself is still valid. This will manifest earliest in kernel invocation because the buffer object is in the best case invalid orin the worst case fails with a segfault.  
  
I think that's not a problem since structure `detail::buffer_iterator_index_expr<T, Expr>` returned by `buffer_iterator::operator[]` should never be stored anywhere. In such a situation its lifetime never exceeds lifetime of the iterator.

---

## Comment 2

> Username: Ulfgard  
> Created at: 2016-10-05 09:33:10 UTC  
> Url: https://github.com/boostorg/compute/issues/665#issuecomment-251627393  

Hi,  
  
for your last comment: the function above gives an example where it is not stored, just forwarded and thus easily exceeds the lifetime of the iterator.  
  
my use case is matrix indexing using a function matrix(x,y)-> vector[x*colums+y] which is required to implement kernels that read tiles of a matrix instead of rows/columns when using meta_kernel. This can not be handled easily with an iterator(just imagine implementing an iterator for a matrix transposition). I worked around this by using my own indexing objects, but only after i did a bug hunt for a few hours.  
  
In general the lifetime of an iterator should never exceed the liftetime of its vector so it should be fine to use some reference/pointer to a buffer object inside the iterator. This also circumvents any lifetime problems regarding the lifetime of the iterator in relation to its reference objects.

---

## Comment 3

> Username: jszuppe  
> Created at: 2016-10-05 10:16:56 UTC  
> Url: https://github.com/boostorg/compute/issues/665#issuecomment-251636445  

> In general the lifetime of an iterator should never exceed the liftetime of its vector so it should be fine to use some reference/pointer to a buffer object inside the iterator. This also circumvents any lifetime problems regarding the lifetime of the iterator in relation to its reference objects.  
  
`buffer_iterator<>` becomes invalid if and only if the buffer object is not longer valid (and it does not matter that it is passed by value, because reference counter is not increased), in that sense it works similarly to the  iterators provided by `std::vector`.  
  
> for your last comment: the function above gives an example where it is not stored, just forwarded and thus easily exceeds the lifetime of the iterator.  
  
Yes, you're right. In that case it's not explicitly stored anywhere, iterator dies and the returned index expression has a invalid const reference to `boost::compute::buffer` object. It was never designed for this, so this problem has never occurred.  I think we can fix it by replacing the const reference with a simple value which does not increase buffer reference counter:  
  
``` cpp  
template<class T, class IndexExpr>  
struct buffer_iterator_index_expr  
{  
    typedef T result_type;  
  
    buffer_iterator_index_expr(const buffer &buffer,  
                               size_t index,  
                               const memory_object::address_space address_space,  
                               const IndexExpr &expr)  
        : m_buffer(buffer.get(), false),  
          m_index(index),  
          m_address_space(address_space),  
          m_expr(expr)  
    {  
    }  
  
// ...  
  
    const buffer m_buffer;  
    size_t m_index;  
    memory_object::address_space m_address_space;  
    IndexExpr m_expr;  
};  
```

---

## Comment 4

> Username: Ulfgard  
> Created at: 2016-10-05 10:40:53 UTC  
> Url: https://github.com/boostorg/compute/issues/665#issuecomment-251640890  

sounds about right! Thanks for the solution

---

## Comment 5

> Username: jszuppe  
> Created at: 2016-10-08 09:27:32 UTC  
> Url: https://github.com/boostorg/compute/issues/665#issuecomment-252414220  

I've prepared the fix and you also need to add destructor to `buffer_iterator_index_expr<>` class:  
  
``` cpp  
    ~buffer_iterator_index_expr()  
    {  
        // set buffer to null so that its reference count will  
        // not be decremented when its destructor is called  
        m_buffer.get() = 0;  
    }  
```

---

## Comment 6

> Username: Ulfgard  
> Created at: 2016-11-09 09:12:40 UTC  
> Url: https://github.com/boostorg/compute/issues/665#issuecomment-259366208  

issue was fixed in #666. This is clsoed for me. Thanks a lot!
