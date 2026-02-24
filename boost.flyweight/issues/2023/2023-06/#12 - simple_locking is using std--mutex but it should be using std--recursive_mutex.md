# #12 - simple_locking is using std::mutex but it should be using std::recursive_mutex [Closed]

> Username: mclow  
> Created at: 2023-06-29 18:24:23 UTC  
> Updated at: 2023-06-30 09:29:09 UTC  
> Closed at: 2023-06-30 09:17:14 UTC  
> Url: https://github.com/boostorg/flyweight/issues/12  

Taken from the boost-users mailing list:  
  
Dear All,  
  
On windows flyweight simple_locking is using std::mutex but it should be using std::recursive_mutex.  
  
I've got this firing an exception due to _Check_C_return() getting passed something else than success (number 3).  
  
I've checked by implementing my own locking policy and it definitely shows that the code is attempting to hold more than a single lock in parallel on the same mutex.  
The secured set.erase() can cause further flyweights to be destroyed given a suitable recursive use of flyweight.

---

## Comment 1

> Username: Flamefire  
> Created at: 2023-06-30 07:01:40 UTC  
> Url: https://github.com/boostorg/flyweight/issues/12#issuecomment-1614219733  

From documentation:  
> The type Locking::mutex_type (or Locking'::mutex_type if (b) applies) is a model of Mutex] and supports recursive locking.  
  
And [simple_locking](https://github.com/boostorg/flyweight/blob/develop/include/boost/flyweight/simple_locking.hpp) doesn't use `std::mutex` but `detail::recursive_lightweight_mutex`  
  
So there doesn't seem to be a bug in this library.

---

## Comment 2

> Username: joaquintides  
> Created at: 2023-06-30 09:18:53 UTC  
> Updated at: 2023-06-30 09:29:09 UTC  
> Url: https://github.com/boostorg/flyweight/issues/12#issuecomment-1614379306  

Hi, this is indeed a regression introduced by [this change in Boost.SmartPtr](https://github.com/boostorg/smart_ptr/commit/169c0cd52a25ece5581ddbb31df60ecc8ade731a). Fixed in 3dc485bfe8726eabebf7c0f055cadcc1ebbe4004. Could you try locally? Thank you!
