# #249 - boost::interprocess::interprocess_mutex is not an implicit lifetime type [Closed]

> Username: jodyhagins  
> Created at: 2025-02-10 15:32:43 UTC  
> Updated at: 2026-01-07 09:26:07 UTC  
> Closed at: 2026-01-07 08:38:25 UTC  
> Url: https://github.com/boostorg/interprocess/issues/249  

We've always known that accessing objects in shared memory were technically UB, but we did it anyway.  
  
The standard has tried to close that hole, and now defines how objects can have their lifetimes managed implicitly, either by `start_lifetime_as` or certain blessed functions like `malloc`, and implementation provided ones like `mmap`.  
  
However, this all mandates that the only objects who can have their lifetime implicitly managed by the abstract machine are those that are implicit lifetime types, i.e., `std::is_implicit_lifetime<T>::value` is `true`.  
  
Unfortunately, this is not true for `boost::interprocess::interprocess_mutex` and most (all?) other Boost.Interprocess types.  
  
For `boost::interprocess::interprocess_mutex`, it has a non-trivial constructor, and deleted copy and move constructors.  This means that there is no way to trivially construct an instance.  Thus, it cannot be an implicit lifetime type, and it is still UB to use one in shared memory, even with all the implicit lifetime mechanisms.  
  
I understand this "works" today, but I don't think it should be ignored going forward.  
  
We can make the default ctor trivial.  But, this will be a breaking change for existing code that relies on the initialization semantics.  
  
We could add a macro, maybe BOOST_INTERPROCESS_IMPLICIT_LIFETIME, which would be an explicit opt-in to make types satisfy the implicit lifetime guarantee.  The user, having explicitly defined that macro, would take the responsibility to make sure they correctly used and initialized the types.  
  
I'd be willing to work on this and submit pull requests, but wanted to submit the suggestion first.  Maybe somebody else has already looked into this.  I didn't see anything in the other issues though.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2026-01-07 08:38:25 UTC  
> Url: https://github.com/boostorg/interprocess/issues/249#issuecomment-3717840178  

I don't this approach is feasible because any shared memory mapping approach is outside the abstract machine. Most Boost.Interprocess utilities placed in shared memory need a constructor and will never be implicit lifetime types.  
  
P0593R6 proposes mmap and similar to be operations that implicitly create objects, but that's not enough to support Boost.Interprocess use cases.

---

## Comment 2

> Username: jodyhagins  
> Created at: 2026-01-07 09:26:07 UTC  
> Url: https://github.com/boostorg/interprocess/issues/249#issuecomment-3717996812  

Yes, they need a constructor, but they don't need a default constructor.  
  
By definition, if a type is not an implicit lifetime type, there is no defined way to use it in shared memory across processes.  
  
Its lifetime can't be implicitly started, nor can its lifetime be explicitly started via `std::start_lifetime_as` because `std::start_lifetime_as` can only be used with an implicit lifetime type.  
  
One way is to define a private, trivial default constructor. This meets the constructor requirements for an implicit-lifetime type, and since it is private, it can help prevent users from accidentally creating an uninitialized instance.
