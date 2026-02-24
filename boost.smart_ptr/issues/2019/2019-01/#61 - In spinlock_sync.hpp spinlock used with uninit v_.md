# #61 - In spinlock_sync.hpp spinlock used with uninit v_ [Closed]

> Username: everett1224  
> Created at: 2019-01-21 09:25:53 UTC  
> Updated at: 2019-04-20 16:11:58 UTC  
> Closed at: 2019-04-20 16:11:58 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/61  

class spinlock  
{  
public:  
  
    int v_;   
  
public:  
  
    bool try_lock()  
    {     
        int r = __sync_lock_test_and_set( &v_, 1 );  
        return r == 0;  
    }     
  
    void lock()  
    {     
        for( unsigned k = 0; !try_lock(); ++k )  
        {     
            boost::detail::yield( k );  
        }     
    }     
  
    void unlock()  
    {     
        __sync_lock_release( &v_ );  
    }     
  
  
why not init v_ in constructor ?

---

## Comment 1

> Username: pdimov  
> Created at: 2019-01-21 15:36:53 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/61#issuecomment-456114351  

The (implementation detail) `spinlock` struct is initialized via aggregate initialization with `BOOST_DETAIL_SPINLOCK_INIT`:  
  
```  
static boost::detail::spinlock sp = BOOST_DETAIL_SPINLOCK_INIT;  
```  
  
in order to avoid initialization order problems. (This is similar to how `pthread_mutex_t` can be initialized with `PTHREAD_MUTEX_INITIALIZER`.)  
  
It's possible in C++11 to use a `constexpr` constructor for static initialization, but `spinlock` predates C++11.
