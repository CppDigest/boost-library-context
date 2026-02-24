# #35 respect alignment of types [Closed]

> Username: nevion  
> Created at: 2015-08-16 12:18:03 UTC  
> Updated at: 2021-05-01 03:00:01 UTC  
> Closed at: 2021-05-01 03:00:01 UTC  
> Url: https://github.com/boostorg/python/pull/35  

see #27

---

## Comment 1

> Username: eudoxos  
> Created_at: 2018-05-06 08:20:08 UTC  
> Url: https://github.com/boostorg/python/pull/35#issuecomment-386862534  

Can I ping this PR a bit?

---

## Comment 2

> Username: stefanseefeld  
> Created_at: 2021-04-17 21:09:35 UTC  
> Url: https://github.com/boostorg/python/pull/35#issuecomment-821888059  

This approach, while a good idea in principle, unfortunately introduces API changes (visible at least to those who use custom converters), and thus can't be accepted as written. (The problematic bit is the change from `storage.bytes` to `storage.address()` introduced through the changed implementation of `aligned_storage`.  
(In addition, if we wanted to change the implementation of the `aligned_storage` type, it might be best to adapt the version from `std::`, and simply add whatever is needed to preserve backward-compatibility.)

---

## Comment 3

> Username: nevion  
> Created_at: 2021-04-17 22:59:44 UTC  
> Url: https://github.com/boostorg/python/pull/35#issuecomment-821899122  

Righto, do nothing for another 6 years in the name of problematic bits and api changes with arbitrary preference driven show stoppers.  
  
What do you think about  doing those changes you want or otherwise deal with the problem rather than a workaround (newing any wrapped object), though?  I don't use boost.python anymore due to it's sloth in matters like these but it does concern me when I see PRs or libraries both take this kind of time and kick the can down indefinitely.

---

## Comment 4

> Username: stefanseefeld  
> Created_at: 2021-04-18 00:17:25 UTC  
> Url: https://github.com/boostorg/python/pull/35#issuecomment-821906856  

This being Free Software, people spend the time they have to contribute, myself included, based on what seems most urgent / important. And since there is a rather simple way to solve the problem of exposing alignment-constrained types to Python, this may not event count as a critical issue.  
If you'd rather see this changed, feel free to engage and collaborate !

---

## Comment 5

> Username: nevion  
> Created_at: 2021-04-18 08:55:57 UTC  
> Url: https://github.com/boostorg/python/pull/35#issuecomment-821957657  

@stefanseefeld   
  
One of the areas I had troubles with the smart pointer approach, which I used in some spaces before I did the work on this PR was with arrays/vectors of objects.  If one were trying to make interfacing  wrappers, forcing smart pointers is not going to work out well - and boost.python should have minimal say on the structural choices one takes on the C++ side.  My point is, you did not suggest a work around, you suggested something that works sometimes for some situations.  I did rather see this changed, and I did engage and collaborate.  I don't know if you were responsible for not providing feedback for 5+ years  but address vs bytes is not something I think you should block the changes on or take issue with - at the very least you should propose something that works for aligned storage vs bytes if you have to keep that the same out of extraordinary care of not changing the more internal apis/layouts.   I don't know , maybe you can use partial template specialization in the case of alignment of 1 to bounce to the original implementations of some things but so far I see very minimal reasons for that in all the private usages.  
  
The definition of aligned_storage is a union of bytes and effectively std::aligned_storage through boost::aligned_storage that offered compatibility in the case where the alignment was 1.  What concrete changes are you seeking to make given that we effectively  used boost's version of std::aligned_storage (is there a reason to bounce?  How are we feeling with c++11+ usage?)  Also with the bytes member present, how did we break existing converters again?  They were already broken if they had > 1 alignment requirements, and regardless what compiled then should compile now.  
  
Alignment is something you should make work out of the box without surprising corner cases if you want boost.python to be wrapping standard C++ - I still believe that even if I'm not an active user of boost.python anymore.  This PR showed several simple changes to get there but but concrete ways to get it in were not offered; I'm not seeing where it wasn't and still isn't ready for mainline use.

---

## Comment 6

> Username: stefanseefeld  
> Created_at: 2021-04-18 15:55:06 UTC  
> Url: https://github.com/boostorg/python/pull/35#issuecomment-822014126  

First the compatibility issue:  
I know a lot of code that relies on the `storage.bytes` member for custom conversion functions (similar to https://github.com/boostorg/python/blob/develop/src/numpy/dtype.cpp#L172), so changing that will break all those. We would at least need to offer a compile-time switch (macro) to let them choose between the old and the new API.  
  
HeldType: Can you give an example of where a smart-pointer HeldType would impact the automatic type conversion available for function calls ? I don't understand your concern there. In fact, if we changed the `Boost.Python` implementation to change the default wrapper object layout, I don't think anyone should notice.  
  
All that being said, I'm confident that we can find a way to fix the alignment for by-value-stored objects without breaking the existing API (`bytes` vs. `address()`).

---

## Comment 7

> Username: nevion  
> Created_at: 2021-04-18 17:19:51 UTC  
> Updated_at: 2021-04-19 07:50:21 UTC  
> Url: https://github.com/boostorg/python/pull/35#issuecomment-822026926  

First - can you call out why you think converters will break?  bytes is still there in structure and in the existing restricted case of wrapped values without updating code for alignment awareness, compatibility is held:  
```  
union aligned_storage_t{  
  typedef typename ::boost::aligned_storage< ::boost::python::detail::referent_size<T>::value, alignment_of<T>::value>::type type;  
  type storage;  
  char bytes[sizeof(type)];  
  
  void * address() const{  
      return storage.address();  
  }  
};  
  
// A metafunction returning a POD type which can store U, where T ==  
// U&. If T is not a reference type, returns a POD which can store T.  
template <class T>  
struct referent_storage  
{  
    typedef aligned_storage_t<T> type;  
    type storage;  
};  
```  
Which means numpy's dtype  line:  
```  
    void * storage = reinterpret_cast<pyconv::rvalue_from_python_storage<T>*>(data)->storage.bytes;  
```  
Should continue to work just fine _unmodified_ for any previously supported type, unless I'm missing something.  
  
Of course I would consider bytes backwards compatibility oriented and deprecated or discouraged in new code, but a user could mix them them in a compilation unit in terms of custom converters and it'd be fine assuming they didn't create bugs with bytes and types of alignment > 1 , the currently already broken case.  So there's no need to complicate this portion further with knobs or macros.  
  
  
As real world driven example of smart-ptrs not cutting it:  
```  
//using avx enabled Eigen  
struct alignas(32) state_t{  
   Eigen::Vector3d<double> position, velocity;  
   Eigen::Matrix<double, 6, 6> covariance;  
};  
  
std::vector<state_t> states(102400);  
//let's expose states to python for numpy/scipy powered tools!  
```  
Obviously we want efficient numerical code adhering to alignment on the c++ side - we wrote it in c++ and made sure we're getting avx, but we also want to be able to interface that with python without issues - python-glue does not get to set the rules on this, this is a case where boost.python would be pushing a whole lot of baggage with smart-pointers only.  There are significant effects to the heap, memory/cache locality to be considered + redundant overheads ( if shared-ptr) is used with smart-pointers here that mean the user has to just figure out another way since the type system is not solving it.

---

## Comment 8

> Username: stefanseefeld  
> Created_at: 2021-04-27 13:56:49 UTC  
> Url: https://github.com/boostorg/python/pull/35#issuecomment-827627150  

I have reworked this PR in https://github.com/boostorg/python/pull/360.  
One reason I decided not to use the `boost::aligned_storage::address()` API is that that isn't part of `std::aligned_storage`. And since everything is already using the `...storage.bytes` from the union above, I don't see the addition of `...storage.address()` as necessary.  
  
I'm not sure I understand your example above as demonstrating that using smart pointers as `HeldType` couldn't work. But it also doesn't really matter, as we now do have a working PR to store such types by-value rather than by-(smart)-pointer.  
  
Please let me know if you think something is missing. (I did include your test case, and could add additional test cases if there are more cases to be considered / tested.)

---
