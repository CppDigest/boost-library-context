# #741 - std::distance on strided iterator runs forever [Closed]

> Username: Ulfgard  
> Created at: 2017-09-09 11:52:27 UTC  
> Updated at: 2017-09-12 07:40:21 UTC  
> Closed at: 2017-09-12 07:40:21 UTC  
> Url: https://github.com/boostorg/compute/issues/741  

Minimal reproducible example on boost 1.64  
  
```  
boost::compute::vector<float> vec(300);  
boost::compute::strided_iterator<boost::compute::buffer_iterator<float> > begin(vec.begin(),1);  
boost::compute::strided_iterator<boost::compute::buffer_iterator<float> > end(vec.end(),1);  
std::distance(end,begin);  
```  
  
This code will run forever. Note that this works perfectly on the buffer iterator alone and it works perfectly when switching begin and end. A rough debugging session reveals that the call above for some reason will choose the default version of std::distance which will iterate over the iterator range. For 64 bit this takes some time.  
  
I came to this issue because for some reason for some iterators a call of begin.distanceTo(end) in the iterator base class is rewritten as -end.distanceTo(begin) somewhere deep in the call stack.

---

## Comment 1

> Username: jszuppe  
> Created at: 2017-09-10 08:04:24 UTC  
> Url: https://github.com/boostorg/compute/issues/741#issuecomment-328326651  

First, I wanted to reference [make_strided_iterator_end](https://github.com/boostorg/compute/blob/master/include/boost/compute/iterator/strided_iterator.hpp#L270) function, which is there to create correct `end`s for `strided_iterator`, but after a moment I've noticed that the stride is equal 1.

---

## Comment 2

> Username: Ulfgard  
> Created at: 2017-09-10 09:21:14 UTC  
> Updated at: 2017-09-10 09:21:36 UTC  
> Url: https://github.com/boostorg/compute/issues/741#issuecomment-328330403  

i would argue that the issue is that strided_iterator uses iterator_adapter and that assigns the iterator_category strict by standard. The issue is that buffer_iterator returns a proxy object instead of a reference and therefore does not even qualify as forward_iterator. so i bet strided_iterator is just a input_iterator which explains everything.

---

## Comment 3

> Username: jszuppe  
> Created at: 2017-09-10 09:37:56 UTC  
> Url: https://github.com/boostorg/compute/issues/741#issuecomment-328331235  

> The issue is that buffer_iterator returns a proxy object instead of a reference and therefore does not even qualify as forward_iterator.  
  
Actually, `buffer_iterator` is tagged with `std::random_access_iterator_tag`.  
  
> so i bet strided_iterator is just a forward_iterator which explains everything.  
  
It looks like it's tagged with `boost::iterators::random_access_traversal_tag` tag, which STL treats like an `InputIterator`.

---

## Comment 4

> Username: Ulfgard  
> Created at: 2017-09-10 09:43:58 UTC  
> Url: https://github.com/boostorg/compute/issues/741#issuecomment-328331519  

exactly. buffer_iterator forces itself the "right" tag, even though it does not qualify for it fully. but iterator_adaptor will check again and rectify this "error". So i bet you have to force the iterator_adaptor to choose the "right" tag as well.

---

## Comment 5

> Username: jszuppe  
> Created at: 2017-09-10 09:51:33 UTC  
> Url: https://github.com/boostorg/compute/issues/741#issuecomment-328331866  

I think it's simpler: the problem is that `boost::iterators::iterator_traversal<Iterator>::type`, which is used by default in `iterator_adaptor` converts STL tags to Boost tags. `std::random_access_iterator_tag` becomes `boost::iterators::random_access_traversal_tag`.  Using `std::iterator_traits<Iterator>::iterator_category` in `strided_iterator` fixes that problem.

---

## Comment 6

> Username: Ulfgard  
> Created at: 2017-09-12 07:35:14 UTC  
> Url: https://github.com/boostorg/compute/issues/741#issuecomment-328766776  

thank you for fixing this. The change looks good. If you want, you can close this!
