# #49 - C++20 Support for auto_buffer [Closed]

> Username: neogenie  
> Created at: 2020-11-27 16:25:38 UTC  
> Updated at: 2020-11-27 16:33:19 UTC  
> Closed at: 2020-11-27 16:33:19 UTC  
> Url: https://github.com/boostorg/signals2/issues/49  

Boost 1.74 Clang 11 macOS  
  
The new standard C++20 https://en.cppreference.com/w/cpp/memory/allocator marks   
  
```  
size_type  
difference_type  
```  
  
as deprecated:  
  
`/usr/local/opt/llvm/include/c++/v1/memory`:  
  
```template <class _Tp>  
class _LIBCPP_TEMPLATE_VIS allocator  
{  
public:  
#if _LIBCPP_STD_VER <= 17 || defined(_LIBCPP_ENABLE_CXX20_REMOVED_ALLOCATOR_MEMBERS)  
    _LIBCPP_DEPRECATED_IN_CXX17 typedef size_t     size_type;  
    _LIBCPP_DEPRECATED_IN_CXX17 typedef ptrdiff_t  difference_type;  
    _LIBCPP_DEPRECATED_IN_CXX17 typedef _Tp*       pointer;  
    _LIBCPP_DEPRECATED_IN_CXX17 typedef const _Tp* const_pointer;  
    _LIBCPP_DEPRECATED_IN_CXX17 typedef _Tp&       reference;  
    _LIBCPP_DEPRECATED_IN_CXX17 typedef const _Tp& const_reference;  
  
    template <class _Up> struct _LIBCPP_DEPRECATED_IN_CXX17 rebind {typedef allocator<_Up> other;};  
#endif  
  
    typedef _Tp value_type;  
  
    typedef true_type propagate_on_container_move_assignment;  
    typedef true_type is_always_equal;  
  
    _LIBCPP_INLINE_VISIBILITY _LIBCPP_CONSTEXPR_AFTER_CXX17  
    allocator() _NOEXCEPT {}  
  
    template <class _Up>  
    _LIBCPP_INLINE_VISIBILITY _LIBCPP_CONSTEXPR_AFTER_CXX17  
    allocator(const allocator<_Up>&) _NOEXCEPT {}  
  
#if _LIBCPP_STD_VER <= 17 || defined(_LIBCPP_ENABLE_CXX20_REMOVED_ALLOCATOR_MEMBERS)  
    _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_INLINE_VISIBILITY  
    pointer address(reference __x) const _NOEXCEPT  
        {return _VSTD::addressof(__x);}  
    _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_INLINE_VISIBILITY  
    const_pointer address(const_reference __x) const _NOEXCEPT  
        {return _VSTD::addressof(__x);}  
#endif  
  
    _LIBCPP_NODISCARD_AFTER_CXX17 _LIBCPP_INLINE_VISIBILITY _Tp* allocate(size_t __n)  
        {  
        // TODO(mpark): Replace with `allocator_traits<allocator>::max_size(*this)`.  
        if (__n > (size_t(~0) / sizeof(_Tp)))  
            __throw_length_error("allocator<T>::allocate(size_t n)"  
                                 " 'n' exceeds maximum supported size");  
        return static_cast<_Tp*>(_VSTD::__libcpp_allocate(__n * sizeof(_Tp), _LIBCPP_ALIGNOF(_Tp)));  
        }  
  
#if _LIBCPP_STD_VER <= 17 || defined(_LIBCPP_ENABLE_CXX20_REMOVED_ALLOCATOR_MEMBERS)  
    _LIBCPP_NODISCARD_AFTER_CXX17 _LIBCPP_INLINE_VISIBILITY _LIBCPP_DEPRECATED_IN_CXX17  
    _Tp* allocate(size_t __n, const void*) { return allocate(__n); }  
#endif  
  
    _LIBCPP_INLINE_VISIBILITY void deallocate(_Tp* __p, size_t __n) _NOEXCEPT  
        {_VSTD::__libcpp_deallocate((void*)__p, __n * sizeof(_Tp), _LIBCPP_ALIGNOF(_Tp));}  
  
#if _LIBCPP_STD_VER <= 17 || defined(_LIBCPP_ENABLE_CXX20_REMOVED_ALLOCATOR_MEMBERS)  
    _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_INLINE_VISIBILITY size_type max_size() const _NOEXCEPT  
        {return size_type(~0) / sizeof(_Tp);}  
  
    template <class _Up, class... _Args>  
        _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_INLINE_VISIBILITY  
        void  
        construct(_Up* __p, _Args&&... __args)  
        {  
            ::new((void*)__p) _Up(_VSTD::forward<_Args>(__args)...);  
        }  
    _LIBCPP_DEPRECATED_IN_CXX17 _LIBCPP_INLINE_VISIBILITY void destroy(pointer __p) {__p->~_Tp();}  
#endif  
};  
```  
  
So compile signal2 library with -std=c++20 got an error:  
  
```  
/usr/local/include/boost/signals2/detail/auto_buffer.hpp:143:37: error: no type named 'size_type' in 'std::__1::allocator<boost::shared_ptr<void>>'  
        typedef typename Allocator::size_type            size_type;  
                ~~~~~~~~~~~~~~~~~~~~^~~~~~~~~  
/usr/local/include/boost/signals2/connection.hpp:53:61: note: in instantiation of template class 'boost::signals2::detail::auto_buffer<boost::shared_ptr<void>, boost::signals2::detail::store_n_objects<10>, boost::signals2::detail::default_grow_policy, std::__1::allocator<boost::shared_ptr<void>>>' requested here  
        auto_buffer<shared_ptr<void>, store_n_objects<10> > garbage;  
```

---

## Comment 1

> Username: glenfe  
> Created at: 2020-11-27 16:33:19 UTC  
> Url: https://github.com/boostorg/signals2/issues/49#issuecomment-734912241  

Already fixed for the upcoming Boost 1.75 release: a2d79e938cc9d58f64e443054ecae220cbe5c0fa
