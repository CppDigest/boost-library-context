# #280 - Keeping bloat under control [Closed]

> Username: psiha  
> Created at: 2024-09-02 15:41:34 UTC  
> Updated at: 2025-04-11 09:09:05 UTC  
> Closed at: 2025-04-11 09:09:03 UTC  
> Url: https://github.com/boostorg/unordered/issues/280  

Please reconsider the possible overuse of forceinlining. For example for unordered_flat_set insert() is fully inlined at each callsite (even with rather complex non-default hashers) except for the unchecked_emplace_with_rehash() part.  
Considering that unordered containers are complex objects that themselves can never reside in registers I'm failing to see how this forceinlining helps anything.  
The only thing I see is the common case when the objects contained are trivial objects that _can_ be passed and returned in registers (as is my use case) - then the generic Args&&... signature would unnecessarily cause them to be passed through memory (ignoring the lucky case you get SRA optimization to kick in) - for this I'd rather some kind of modernized boost::call_traits-like mechanism be used.  
  
On a related note - consider skipping double key construction from ...args - rather move the key (constructed with key_from at the beginning of emplace_impl) and peel of ...args used for key construction before forwarding them to unchecked_emplace*.  
  
Also could you not extract the construction from unchecked_emplace_at() and unchecked_emplace_with_rehash() - make them only allocate the space/positon and simply do an inplace construction at one place at the end of emplace_impl()? (that way you wouldn't have to forward Args&& around at all)

---

## Comment 1

> Username: joaquintides  
> Created at: 2024-09-03 15:27:10 UTC  
> Updated at: 2024-09-03 15:28:24 UTC  
> Url: https://github.com/boostorg/unordered/issues/280#issuecomment-2326815874  

> Please reconsider the possible overuse of forceinlining. For example for unordered_flat_set insert() is fully inlined at each callsite (even with rather complex non-default hashers) except for the unchecked_emplace_with_rehash() part.  
Considering that unordered containers are complex objects that themselves can never reside in registers I'm failing to see how this forceinlining helps anything.  
  
The fact that the containers are complex objects much larger than a register does not have anything to do with inlining their member functions (such as `insert`). The container itself (i.e. `*this`) will be passed/used by reference, it's the implementation code of `insert` that gets inlined inside the caller's code. We forced inlining because it actually improved performance for some compilers that didn't inline automatically in some cases.  
  
> The only thing I see is the common case when the objects contained are trivial objects that can be passed and returned in registers (as is my use case) - then the generic Args&&... signature would unnecessarily cause them to be passed through memory (ignoring the lucky case you get SRA optimization to kick in) - for this I'd rather some kind of modernized boost::call_traits-like mechanism be used.  
  
Usually, the compiler is smart enough to decide whether the arguments of an inlined function are treated as references or passed around as values, regardless of what the function signature says --this is one of the benefits of inlining. Consider [this example](https://godbolt.org/z/M71eeadnK): when no optimizations are applied, `foo` and `bar` are indeed different (the former accepts its arg by reference), but with `-O3` all differences disappear.  
  
> On a related note - consider skipping double key construction from ...args - rather move the key (constructed with key_from at the beginning of emplace_impl) and peel of ...args used for key construction before forwarding them to unchecked_emplace*.  
  
The key is not constructed twice from args ever, although an examination of `emplace_impl` in isolation may suggest otherwise. The critical part is in the implementation of `emplace`, the entry function that later calls `emplace_impl`:  
  
https://github.com/boostorg/unordered/blob/c1317cb5bee0057712c31f52667b6c0852745c16/include/boost/unordered/detail/foa/table.hpp#L406-L422  
  
In the first case, an object `x` is constructed from args that holds the key, and this key is moved to its final destination upon successful insertion. If `emplace` is passed a key directly (second case), then we omit any kind of construction and pass the key along.  
  
> Also could you not extract the construction from unchecked_emplace_at() and unchecked_emplace_with_rehash() - make them only allocate the space/positon and simply do an inplace construction at one place at the end of emplace_impl()? (that way you wouldn't have to forward Args&& around at all)  
  
This is in principle doable, but:  
  
* It'd probably get us nothing in terms of performance or binary size.  
* It'd complicate things extraordinarily if construction fails (i.e. it throws an exception), as we'd need to undo all the insertion work; by contrast, `unchecked_emplace_at` and `unchecked_emplace_with_rehash` call `nosize_unchecked_emplace_at`, which only makes permanent changes to the data structure _after_ construction is successful:  
  
https://github.com/boostorg/unordered/blob/c1317cb5bee0057712c31f52667b6c0852745c16/include/boost/unordered/detail/foa/core.hpp#L2340-L2360

---

## Comment 2

> Username: psiha  
> Created at: 2025-01-09 15:30:48 UTC  
> Updated at: 2025-01-09 16:16:44 UTC  
> Url: https://github.com/boostorg/unordered/issues/280#issuecomment-2580571817  

> The fact that the containers are complex objects much larger than a register does not have anything to do with inlining their member functions (such as insert). The container itself (i.e. *this) will be passed/used by reference...  
  
Exactly - that function arguments, including the implicit *this, are passed and used by reference (forcing stack bouncing) instead of by value is IMO/experience one of the main sources of a function call overhead/impetus for forced inlining (other things being equal, like the function body still being visible to the compiler for IPO), the other being parts of the functions internal results which can be memoized across multiple calls (e.g. in a loop or a close by call site). And yes this includes *this (one of the motivating examples for/in the deducing this proposal is the ability to pass *this/self by value) - imagine an object consisting of a few pointers that could be otherwised by passed through registers but cannot when it is accessed through a member function (because this forces the creation of a this pointer which obviously cannot 'point to registers').  
  
One way of mitigating this problem (for the this pointer) is for a member function to be a simple wrapper that delegates to a static function which accepts all of the arguments (for which it makes, given the type, ABI etc) by value, now we also have the deducing this/explicit object parameter pass-by-val syntax to make things easier.  
  
Anyways - my point was that unordered containers are complex objects that will always reside in memory and thus their mem.funcs. should not need to be forceinlined (for any measurable speedup).  
  
  
> ...it's the implementation code of insert that gets inlined inside the caller's code. We forced inlining because it actually improved performance for some compilers that didn't inline automatically in some cases.  
  
I realize that - was just trying to question/get at the rationale and root cause - perhaps the source of the call overhead (be it stack bouncing or recalculation of a memoizable function prologue) could be mitigated with a less brute-force/'bloating' approach...  
  
(ps. sorry for the long delay:)

---

## Comment 3

> Username: psiha  
> Created at: 2025-01-09 15:43:05 UTC  
> Updated at: 2025-01-09 16:16:16 UTC  
> Url: https://github.com/boostorg/unordered/issues/280#issuecomment-2580603413  

> Usually, the compiler is smart enough to decide whether the arguments of an inlined function are treated as references or passed around as values, regardless of what the function signature says --this is one of the benefits of inlining. Consider [this example](https://godbolt.org/z/M71eeadnK): when no optimizations are applied, foo and bar are indeed different (the former accepts its arg by reference), but with -O3 all differences disappear.  
  
The differences disappear because the function calls - the very thing we are examining - disappear (as they are well forceinlined).  
As mentioned in the opening post, some compilers (GCC and Clang) can, 'when the weather is nice', do that even for noninlined functions, through the (I)SRA optimization - they literaly change the function's ABI (you can see that in your simple godbolt example by making the function that takes the parameter by reference noinline and have internal linkage - GCC and Clang will generate the same code for foo and bar w/ O3). Other compilers, of course we are looking at you MSVC, do not have this optimization.  
  
So, my point was to arrive at a design that would not require sunshine and an inteligent optimizer or forceinlining - unfortunately the state of the language does not help us much here - but I did come up with something that I used in my vm library (exactly for container code):  
[pass_in_reg machinery](https://github.com/psiha/vm/blob/master/include/psi/vm/containers/abi.hpp) used for example [here](https://github.com/psiha/vm/blob/master/include/psi/vm/containers/b%2Btree.hpp#L2038) ... feel free to comment/reuse...

---

## Comment 4

> Username: psiha  
> Created at: 2025-01-09 16:15:58 UTC  
> Updated at: 2025-01-09 16:17:39 UTC  
> Url: https://github.com/boostorg/unordered/issues/280#issuecomment-2580699250  

> In the first case, an object x is constructed from args that holds the key, and this key is moved to its final destination upon successful insertion.  
  
In case of a 'compatible key' (e.g. string_view -> string) this creates the key object even for failed (preexisting) emplacements (minor issue) + (more importantly) SBO based types (like std::string) have nontrivial moves. This could be avoided with delayed construction.  
  
  
> It'd probably get us nothing in terms of performance or binary size.  
  
Oh it would - 'suddenly' the container setup and allocation parts would no longer depend on Key, T, Args... (w/e) - reusable and no need to inline them...  
  
> It'd complicate things extraordinarily if construction fails (i.e. it throws an exception), as we'd need to undo all the insertion work; by contrast, unchecked_emplace_at and unchecked_emplace_with_rehash call nosize_unchecked_emplace_at, which only makes permanent changes to the data structure after construction is successful:`  
  
This only applies for cases where construction can throw, but even for those cases you can simply split the Key/T/Args independent path in two: setup-and-allocation and 'make changes permanent postprocess', i.e.  
```  
  auto position{ setup_and_allocate(); }  
  construct_at( position, args... );  
  commit( position );  
```  
  
for the cases where construction is noexcept you'd just do  
```  
  auto position{ setup_allocate_and_commit(); }  
  construct_at( position, args... );  
```

---

## Comment 5

> Username: joaquintides  
> Created at: 2025-01-10 10:19:36 UTC  
> Url: https://github.com/boostorg/unordered/issues/280#issuecomment-2582295351  

>   > In the first case, an object x is constructed from args that holds the key, and this key is moved to its final destination upon successful insertion.  
>  
> In case of a 'compatible key' (e.g. string_view -> string) this creates the key object even for failed (preexisting) emplacements (minor issue) + (more importantly) SBO based types (like std::string) have nontrivial moves. This could be avoided with delayed construction.  
  
This is generally not possible. Consider the following:  
  
```cpp  
boost::unordered_flat_map<std::string, int, transparent_hash, std::less<>> m;  
std::string_view x("hello");  
m.emplace(x, 0);  
```  
This could be made to work by delaying the construction of the `std::string` key from `x` because `x` acts as a compatible key from the point of view of `transparent_hash` and `std::less<>`. So far, so good. But what about this?  
  
```cpp  
std::allocator<char> al;  
m.emplace(al, 0);  
```  
  
Now, `al` can't be used as a key, and if the code delayed construction of the `std::string` key then it would simply fail to compile. There is no way for the library to know in which cases the `k` argument in `emplace(k, v)` can be legitimately used as a compatible key in the context of heterogeneous lookup.

---

## Comment 6

> Username: joaquintides  
> Created at: 2025-01-10 11:15:53 UTC  
> Updated at: 2025-01-10 11:16:21 UTC  
> Url: https://github.com/boostorg/unordered/issues/280#issuecomment-2582465943  

> for the cases where construction is noexcept you'd just do  
>  
> ```cpp  
>  auto position{ setup_allocate_and_commit(); }  
>  construct_at( position, args... );  
>```  
  
The modifications needed to protoype this case are simple enough:  
  
```cpp  
  template<typename... Args>  
  locator nosize_unchecked_emplace_at(  
    const arrays_type& arrays_,std::size_t pos0,std::size_t hash,  
    Args&&... args)  
  {  
    auto loc=nosize_noconstruct_unchecked_emplace_at(arrays_,pos0,hash);  
    // WARNING: element construction assumed not to throw  
    construct_element(loc.p,std::forward<Args>(args)...);  
    return loc;  
  }  
  
  locator nosize_noconstruct_unchecked_emplace_at(  
    const arrays_type& arrays_,std::size_t pos0,std::size_t hash)  
  {  
    for(prober pb(pos0);;pb.next(arrays_.groups_size_mask)){  
      auto pos=pb.get();  
      auto pg=arrays_.groups()+pos;  
      auto mask=pg->match_available();  
      if(BOOST_LIKELY(mask!=0)){  
        auto n=unchecked_countr_zero(mask);  
        auto p=arrays_.elements()+pos*N+n;  
        pg->set(n,hash);  
        BOOST_UNORDERED_ADD_STATS(cstats.insertion,(pb.length()));  
        return {pg,n,p};  
      }  
      else pg->mark_overflow(hash);  
    }  
  }  
```  
If you're willing to test this in the context of your project and report back measured improvements/pessimizations wrt to binary size and performance, we could use that data to make an informed decision.

---

## Comment 7

> Username: joaquintides  
> Created at: 2025-04-11 09:09:03 UTC  
> Url: https://github.com/boostorg/unordered/issues/280#issuecomment-2796307219  

Closed as OP provided no further feedback.
