# #34 Only use allocator construct/destroy for value_type (not shared_count) [Merged]

> Username: glenfe  
> Created at: 2017-02-28 12:45:44 UTC  
> Updated at: 2017-03-02 04:13:51 UTC  
> Merged at: 2017-03-02 04:06:54 UTC  
> Closed at: 2017-03-02 04:06:54 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/34  

Since DR 2070 was first posted, the pattern for using allocators for construct and destroy has changed to only use them for the value_type (and placement new for all other node types).  
  
Also only use allocate(n) instead of allocate(n, nullptr) since that former should be sufficient for C++98 and beyond.

---

## Comment 1

> Username: pdimov  
> Created_at: 2017-02-28 13:51:21 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/34#issuecomment-283043944  

I'm not sure what "only use them for the value_type" means in this context.

---

## Comment 2

> Username: glenfe  
> Created_at: 2017-02-28 14:31:30 UTC  
> Updated_at: 2017-02-28 14:56:53 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/34#issuecomment-283054018  

Peter, what I mean is: Currently, you're using A's construct() and destroy() to construct and destroy the internal count objects, when you do not need to use them (i.e. placement new is sufficient). After reading the updated conversation in Defect Report 2070, I think now the only place where they are required to be used is for allocate_shared`<T>`(args..) when constructing and destroying T objects.  
  
i.e. I read this as analogous to say, std::list`<T, A>`, using A's construct() and destroy() only for T objects (and using placement new for its node objects.

---

## Comment 3

> Username: pdimov  
> Created_at: 2017-02-28 14:55:36 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/34#issuecomment-283060775  

My confusion was caused by the fact that when using `shared_ptr::shared_ptr(P, D, A)`, the only allocation done by `A` is of the internal node, but you're right that when using `allocate_shared<X>`, there is a `value_type`. It's a bit odd because we'll use the provided allocator to allocate the internal node but to construct `X` inside of it, but this is similar to what `std::list` does.  
  
Sounds reasonable.  
  
Would you please add a test that fails before this change and passes after it?

---

## Comment 4

> Username: glenfe  
> Created_at: 2017-02-28 14:56:19 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/34#issuecomment-283060964  

Will do.

---

## Comment 5

> Username: pdimov  
> Created_at: 2017-02-28 20:31:45 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/34#issuecomment-283152336  

Wouldn't it be easier if the allocator just had a `construct` member that can only construct the `value_type`? It seems to me that this would be closer to something that could potentially occur in practice. (Although it's still contrived. I have trouble thinking of reasonable real code that would be affected.)

---

## Comment 6

> Username: glenfe  
> Created_at: 2017-02-28 21:27:08 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/34#issuecomment-283165257  

Sure, I'll update the test accordingly.

---

## Comment 7

> Username: pdimov  
> Created_at: 2017-03-01 07:28:25 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/34#issuecomment-283265806  

What I meant was, when using `shared_ptr<X> p( new X(v), D(), A<X>() )` and `allocate_shared<X>( A<X>(), v)`, make `A<T>::construct` only work for `X`, instead of `T`: `void A<T>::construct( X* p, int v );`  
  
So it will fail at compile time today, not at runtime.  
  
Still not sure who'll write such an allocator, but, well.

---

## Comment 8

> Username: glenfe  
> Created_at: 2017-03-01 11:52:06 UTC  
> Updated_at: 2017-03-01 16:31:37 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/34#issuecomment-283320738  

Ah, I see now. Makes sense. The new test would now fail to compile without the new change.

---

## Comment 9

> Username: pdimov  
> Created_at: 2017-03-01 14:17:48 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/34#issuecomment-283351415  

Actually, no, I was wrong. `allocator_traits` checks whether `construct` is valid and if it isn't, calls `::new`. So allocators that can't construct `impl_type` work today.  
  
I'll have to think about this a bit more.

---

## Comment 10

> Username: glenfe  
> Created_at: 2017-03-01 14:28:22 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/34#issuecomment-283354218  

Sure, but the test allocator's construct will still be invoked for other types by allocator_traits.  i.e. `creator<int>()` is suitable for allocating any type, but only suitable for constructing int. So it will fail to compile if it was used to try and construct the impl_type.

---

## Comment 11

> Username: pdimov  
> Created_at: 2017-03-01 16:03:00 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/34#issuecomment-283382692  

When I try this allocator, it compiles without the change:  
  
```  
template<class T> class A  
{  
public:  
  
	typedef T value_type;  
  
	A()  
	{  
	}  
  
	template<class Y> A( A<Y> const & )  
	{  
	}  
  
	T * allocate( std::size_t n )  
	{  
		return static_cast< T* >( ::operator new( n * sizeof( T ) ) );  
	}  
  
	void deallocate( T * p, std::size_t /*n*/ )  
	{  
		::operator delete( p );  
	}  
  
	void construct( X * p, int v )  
	{  
		::new( static_cast<void*>( p ) ) X( v );  
	}  
  
	void destroy( X * p )  
	{  
		p->~X();  
	}  
};  
```

---

## Comment 12

> Username: glenfe  
> Created_at: 2017-03-01 16:29:31 UTC  
> Updated_at: 2017-03-01 20:52:25 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/34#issuecomment-283391190  

Right, with your allocator, since there is no suitable construct(), placement new will be invoked. The allocator I wrote in the test is the equivalent of changing your allocator's construct and destroy to be:  
```  
template<class... Args>  
void construct(T* p, Args&&... args)  
{  
    new(p) X(std::forward<Args>(args)...);  
}  
void destroy(T* p)  
{  
    p->~X();  
}  
```  
i.e. allocator_traits would still call them, but the call fails to compile since p is an impl_type pointer, and X is not impl_type.

---

## Comment 13

> Username: pdimov  
> Created_at: 2017-03-01 16:39:47 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/34#issuecomment-283394637  

This will indeed fail but I don't think that anyone writes such code (except by mistake). I was looking for an example that someone might realistically have written, and this isn't it.

---

## Comment 14

> Username: glenfe  
> Created_at: 2017-03-01 17:04:20 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/34#issuecomment-283402466  

A realistic compilation failing case is probably unlikely given the use of allocator_traits.   
  
The practice of the library not using construct() and destroy() for other constructions does seem like a sensible one though. A user designing the allocator should expect it to be only used by the library only for construction of the "element" objects to potentially do something meaningful by providing construct and destroy).  
  
e.g. Even if construct is invoked via allocator_traits by the library for all constructions, it isn't enough for the user to simply provide only construct(int*) to track all int elements, because it might have been used to construct some int used for internal book keeping.

---

## Comment 15

> Username: pdimov  
> Created_at: 2017-03-01 17:26:43 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/34#issuecomment-283409056  

So the requirement then is that in `allocate_shared<X[]>( A(), n, args... )`, `A::construct` needs to be called exactly `n` times to construct the elements of the array, no more and no less. Similarly for `X[N]` and the scalar cases.  
  
In this case your original test was closer to what we need. Sorry for wasting your time.

---

## Comment 16

> Username: glenfe  
> Created_at: 2017-03-01 17:34:55 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/34#issuecomment-283411473  

No problem; I'll update the pull request shortly.

---

## Comment 17

> Username: glenfe  
> Created_at: 2017-03-01 23:18:02 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/34#issuecomment-283503571  

Added array versions to the test case, also. Let me know if anything else is needed.

---

## Comment 18

> Username: pdimov  
> Created_at: 2017-03-02 02:36:44 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/34#issuecomment-283538652  

This looks ready to merge, but just to be sure, are the changes to `allocate_shared_array.hpp` intended to be part of this PR?

---

## Comment 19

> Username: glenfe  
> Created_at: 2017-03-02 03:41:38 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/34#issuecomment-283547377  

Intended - I was going to commit them right after this change goes in, anyway.

---

## Comment 20

> Username: glenfe  
> Created_at: 2017-03-02 04:13:11 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/34#issuecomment-283551321  

Thanks!

---
