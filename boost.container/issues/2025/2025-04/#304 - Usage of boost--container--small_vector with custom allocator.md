# #304 - Usage of boost::container::small_vector with custom allocator [Closed]

> Username: BoyeGuillaume  
> Created at: 2025-04-21 16:48:57 UTC  
> Updated at: 2025-10-27 22:55:32 UTC  
> Closed at: 2025-10-27 22:55:32 UTC  
> Url: https://github.com/boostorg/container/issues/304  

Hello,  
  
I cannot find a way to make use of `boost::container::small_vector` using a custom  allocator. For some reason the small_vector constructor with the argument corresponding to the allocator is not matched. I am using boost version `1.87` with `C++20` on `MSVC`.  
  
```cpp  
  
// Somewhere within the code  
boost::container::small_vector<uint64_t, 32, allocator<uint64_t>  
   _buffer(&allocation_callbacks); /* allocator constructible form allocation_callbacks*) */  
```  
  
This snipper of code works when using `boost::container::vector` (with my allocator) or `boost::container::small_vector` without my allocator but I cannot find a way to make this works. I have also tried to make use of the `small_vector<..>(const allocator&)` constructor but it doesn't work either. It seems that the `small_vector` have a container that takes `const boost::container::small_vector_allocator<T, my_allocator<T>, Options>` but in the documentation of small_vector_allocator it is said that this should not be used directly by the user...   
  
Here is my allocator. (if you are interrested)  
```hpp  
template <typename T>  
class allocator {  
 private:  
  template <typename T>  
  friend struct hy::allocator;  
  hy::allocation_callback* _callback;  
  
 public:  
  typedef T value_type;  
  typedef T* pointer;  
  typedef const T* const_pointer;  
  typedef typename detail::reference_or_void<T>::type reference;  
  typedef typename detail::const_reference_or_void<T>::type const_reference;  
  typedef std::size_t size_type;  
  typedef std::ptrdiff_t difference_type;  
  
  template <class T2>  
  struct rebind {  
    typedef hy::allocator<T2> other;  
  };  
  
  /* Conversion operator to hy::allocator<U> */  
  template <typename U>  
  inline operator hy::allocator<U>() const noexcept {  
    return hy::allocator<U>(_callback);  
  }  
  
  inline allocator(hy::allocation_callback* allocation) noexcept  
      : _callback(allocation) {  
    ASSERT(allocation != nullptr);  
  }  
  
  inline allocator(const allocator& other) noexcept  
      : _callback(other._callback) {  
    ASSERT(other._callback != nullptr);  
  }  
  
  inline allocator(allocator&& other) noexcept : _callback(other._callback) {  
    ASSERT(other._callback != nullptr);  
  }  
  
  inline allocator& operator=(const allocator& other) noexcept {  
    ASSERT(other._callback != nullptr);  
    if (this != &other) {  
      _callback = other._callback;  
    }  
    return *this;  
  }  
  
  inline allocator& operator=(allocator&& other) noexcept {  
    ASSERT(other._callback != nullptr);  
    if (this != &other) {  
      _callback = other._callback;  
    }  
    return *this;  
  }  
  
  pointer allocate(size_type n, size_type alignment = alignof(T)) {  
    pointer output = static_cast<T*>(_callback->pfnAllocation(  
        _callback->pUserData, n * sizeof(T), alignment));  
    if (output == nullptr) {  
      throw std::bad_alloc();  
    }  
    return output;  
  }  
  
  void deallocate(pointer p, size_type /* n */) noexcept {  
    try {  
      _callback->pfnFree(_callback->pUserData, p);  
    } catch (...) {  
      ASSERT(false, "Failed to deallocate memory");  
    }  
  }  
  
  pointer reallocate(T* p, size_type n, std::size_t alignment = alignof(T)) {  
    pointer output = static_cast<T*>(_callback->pfnReallocation(  
        _callback->pUserData, p, n * sizeof(T), alignment));  
    if (output == nullptr) {  
      throw std::bad_alloc();  
    }  
    return output;  
  }  
  
  inline size_type max_size() const noexcept {  
    return std::numeric_limits<size_type>::max() / (2 * sizeof(T));  
  }  
  
  // Equality operator (needed for standard compliance)  
  bool operator==(const hy::allocator<T>& other) const noexcept {  
    return _callback == other._callback;  
  }  
  
  bool operator!=(const hy::allocator<T>& other) const noexcept {  
    return !operator==(other);  
  }  
};  
```  
   
Thank you for your help.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2025-10-27 22:55:24 UTC  
> Url: https://github.com/boostorg/container/issues/304#issuecomment-3453640385  

Many thanks for the report. As diagnosed, the `allocator_type` is not correctly defined, it shouldn't be `small_vector_allocator`, as this type is an implementation detail.
