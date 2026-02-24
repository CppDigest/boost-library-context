# #732 - shrink_to_fit() doesn't seem to do anything [Closed]

> Username: BrettRyland  
> Created at: 2017-06-15 13:01:17 UTC  
> Updated at: 2017-07-10 21:50:10 UTC  
> Closed at: 2017-07-10 21:50:10 UTC  
> Url: https://github.com/boostorg/compute/issues/732  

Perhaps I'm doing something wrong, but shrink_to_fit() doesn't seem to do anything. For example, with an appropriate `context` and `queue` the following  
  
    compute::vector<double> device_vec(10000, context);  
    std::cout << "Size and capacity are " << device_vec.size() << " and " << device_vec.capacity() << "\n";  
    device_vec.clear();  
    std::cout << "Size and capacity after clearing are " << device_vec.size() << " and " << device_vec.capacity() << "\n";  
    device_vec.shrink_to_fit(queue);  
    queue.finish();  
    std::cout << "Size and capacity after shrinking to fit are " << device_vec.size() << " and " << device_vec.capacity() << "\n";  
  
gives  
  
    Size and capacity are 10000 and 10000  
    Size and capacity after clearing are 0 and 10000  
    Size and capacity after shrinking to fit are 0 and 10000  
  
which can lead to unexpected memory allocation problems.

---

## Comment 1

> Username: jszuppe  
> Created at: 2017-06-15 13:04:24 UTC  
> Updated at: 2017-06-15 15:00:56 UTC  
> Url: https://github.com/boostorg/compute/issues/732#issuecomment-308723370  

Indeed, it does nothing: https://github.com/boostorg/compute/blob/master/include/boost/compute/container/vector.hpp#L449, but it fulfils the description of that function known from `std::vector`:   
  
> It is a non-binding request to reduce capacity() to size(). It depends on the implementation if the request is fulfilled.  
  
It should be easy to implement behaviour that actually change the size.

---

## Comment 2

> Username: jszuppe  
> Created at: 2017-06-15 14:58:57 UTC  
> Url: https://github.com/boostorg/compute/issues/732#issuecomment-308760660  

However, `resize(size_type size, command_queue &queue)` does work.

---

## Comment 3

> Username: BrettRyland  
> Created at: 2017-06-15 16:19:08 UTC  
> Url: https://github.com/boostorg/compute/issues/732#issuecomment-308792438  

Resizing behaves as expected from the STL, i.e. the number of elements in the vector are changed, but the capacity is never reduced, so that's not an issue.    
While the request to reduce the capacity is non-binding, it would be nice if it was fulfilled. ;-)

---

## Comment 4

> Username: jszuppe  
> Created at: 2017-06-15 16:24:40 UTC  
> Url: https://github.com/boostorg/compute/issues/732#issuecomment-308793927  

> Resizing behaves as expected from the STL, i.e. the number of elements in the vector are changed, but the capacity is never reduced, so that's not an issue.  
  
Oh, yeah, you're right.  
  
> While the request to reduce the capacity is non-binding, it would be nice if it was fulfilled. ;-)  
  
Sure :) I guess nobody's used it or needed it before. If want you can fix it on your own and add a pull request. Otherwise, I can implement it tomorrow or this weekend.

---

## Comment 5

> Username: BrettRyland  
> Created at: 2017-06-15 17:07:59 UTC  
> Url: https://github.com/boostorg/compute/issues/732#issuecomment-308806527  

I'm fairly new to boost::compute, so it's probably best if someone else fixes it. No hurry though. I only came across the problem while experimenting with how many and how large a vector I could allocate on a device https://stackoverflow.com/questions/44547038/allocating-large-vectors-in-boostcompute

---

## Comment 6

> Username: jszuppe  
> Created at: 2017-06-15 17:09:10 UTC  
> Url: https://github.com/boostorg/compute/issues/732#issuecomment-308806999  

Yes, I know :) I answered your questions.
