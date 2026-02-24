# #75 - Moving signals2::signal objects leaves them in an invalid state. [Closed]

> Username: bangerth  
> Created at: 2024-03-23 00:18:21 UTC  
> Updated at: 2024-04-20 02:07:38 UTC  
> Closed at: 2024-04-20 02:07:38 UTC  
> Url: https://github.com/boostorg/signals2/issues/75  

The C++ standard expects that a moved-from object remains in a valid state, though undefined. Typically this is understood to mean that one can still call member functions on it, and that they would act as if one were left with a default-constructed object. This is not the case for `signals2::signal` objects.  
  
Specifically, take this test case:  
```  
int main ()  
{  
    boost::signals2::signal<void ()> a;  
    boost::signals2::signal<void ()> b(std::move(a));    // move constructor; but bug also happens with move assignment  
  
    a.empty();  
    b.empty();  
```  
Here, the call to `a.empty()` fails with an internal assertion:  
```  
disconnect_02: /usr/include/boost/smart_ptr/shared_ptr.hpp:722: typename boost::detail::sp_dereference<T>::type boost::shared_ptr<T>::operator*() const [with T = boost::signals2::detail::signal_impl<void(), boost::signals2::optional_last_value<void>, int, std::less<int>, boost::function<void()>, boost::function<void(const boost::signals2::connection&)>, boost::signals2::mutex>; typename boost::detail::sp_dereference<T>::type = boost::signals2::detail::signal_impl<void(), boost::signals2::optional_last_value<void>, int, std::less<int>, boost::function<void()>, boost::function<void(const boost::signals2::connection&)>, boost::signals2::mutex>&]: Assertion `px != 0' failed.  
```  
This is because of this part of the implementation in https://github.com/boostorg/signals2/blob/4d994bbed820882059c7842ba5e230a26d68f2f0/include/boost/signals2/detail/signal_template.hpp#L676-L694:  
```  
      BOOST_SIGNALS2_SIGNAL_CLASS_NAME(BOOST_SIGNALS2_NUM_ARGS)(  
        BOOST_SIGNALS2_SIGNAL_CLASS_NAME(BOOST_SIGNALS2_NUM_ARGS) && other) BOOST_NOEXCEPT  
      {  
        using std::swap;  
        swap(_pimpl, other._pimpl);  
      }  
        
      BOOST_SIGNALS2_SIGNAL_CLASS_NAME(BOOST_SIGNALS2_NUM_ARGS) &   
        operator=(BOOST_SIGNALS2_SIGNAL_CLASS_NAME(BOOST_SIGNALS2_NUM_ARGS) && rhs) BOOST_NOEXCEPT  
      {  
        if(this == &rhs)  
        {  
          return *this;  
        }  
        _pimpl.reset();  
        using std::swap;  
        swap(_pimpl, rhs._pimpl);  
        return *this;  
      }  
```  
Here, in the move assignment operator (which is indirectly called by the move constructor), we reset `_pimpl`, which is then swapped into the *moved-from* object. If we later call any member function on the moved-from object, it tries to access its `_pimpl` object, which is now a `nullptr` and no good thing will happen.   
  
The fix is to not reset `_pimpl` to a `nullptr` before swapping, but to reset it to a default-constructed `impl_class` pointer.  
  
  
(Why do I need this? I have a class that has a `signals2::signal` member variable. This class is movable, but in the destructor of my class I need to call the signal that everyone connected to this object needs to disengage because the object is going away. To this end, I call the signal. Of course, if the current object has been moved-from, the signal has also been moved. It is a reasonable expectation then that call `operator()` on the signal variable will simply not do anything at all, but in actual fact it triggers the assertion above. The problem is that there is no way to avoid this: I cannot ask the moved-from signal object whether it is still valid, for example by calling `empty()` as above, so can not detect the case where I should avoid calling the signal.)

---

## Comment 1

> Username: fmhess  
> Created at: 2024-03-23 02:52:42 UTC  
> Url: https://github.com/boostorg/signals2/issues/75#issuecomment-2016319139  

Can you work around by default constructing a signal yourself then using swap() instead of move() ?  I'm not super keen on adding overhead to the move constructor/assignment since they are supposed to be fast.  I guess a mostly unusable "null signal" state could be explicitly defined, with an added null() query to check if the signal is in the moved-from state.

---

## Comment 2

> Username: bangerth  
> Created at: 2024-03-23 03:31:26 UTC  
> Url: https://github.com/boostorg/signals2/issues/75#issuecomment-2016328619  

I work around it by explicitly keeping track whether the object has been moved from: https://github.com/bangerth/SampleFlow/pull/225 But in the end, asking everyone who runs into this problem to find a workaround isn't a good solution either -- not the least because it incurs the overhead in user code you want to avoid in the library. All I think everyone wants to write is  
```  
  /**  
   * Move constructor.  
   */  
  MyClass (MyClass &o) = default;  
```  
which currently just doesn't work.  
  
I think the right solution is to replace the  
```  
  _pimpl.reset();  
```  
by something like   
```  
  _pimpl->clear();  
```  
(assuming that `impl_class` has (or could gain) a `clear()` function that just clears what's currently stored. This does not actually incur an additional cost: The `.reset()` call currently has to release any data stored in the `impl_class` instance anyway before de-allocating the memory. Calling `clear()` here releases the data now, and de-allocating the memory just happens later when the moved-from object is destroyed.

---

## Comment 3

> Username: fmhess  
> Created at: 2024-03-23 04:06:14 UTC  
> Url: https://github.com/boostorg/signals2/issues/75#issuecomment-2016337318  

That would only work for move assignment.  For the move constructor, there is no preexisting pimpl to swap into the moved-from signal.

---

## Comment 4

> Username: bangerth  
> Created at: 2024-03-24 00:48:43 UTC  
> Url: https://github.com/boostorg/signals2/issues/75#issuecomment-2016648176  

That's true. Is the construction of an empty object that expensive? Alternatively, of course, one could guard all accesses to `_pimpl` by checking whether it is not a `nullptr`, but that seems like a more expensive approach.

---

## Comment 5

> Username: fmhess  
> Created at: 2024-03-25 18:31:12 UTC  
> Url: https://github.com/boostorg/signals2/issues/75#issuecomment-2018649193  

signals aren't even default constructible unless the Combiner and GroupCompare are default constructible.

---

## Comment 6

> Username: fmhess  
> Created at: 2024-04-02 15:13:29 UTC  
> Url: https://github.com/boostorg/signals2/issues/75#issuecomment-2032330689  

Added "null" query to signal class on develop branch, commit d3545d16dba3ee879eac7ba88a755b8de07173f8

---

## Comment 7

> Username: bangerth  
> Created at: 2024-04-03 19:33:02 UTC  
> Url: https://github.com/boostorg/signals2/issues/75#issuecomment-2035424954  

Yes, that's great. Thank you!

---

## Comment 8

> Username: fmhess  
> Created at: 2024-04-20 02:07:38 UTC  
> Url: https://github.com/boostorg/signals2/issues/75#issuecomment-2067513507  

Merged to master branch.
