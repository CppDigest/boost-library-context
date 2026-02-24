# #308 - concurrent_{node,flat}_set::emplace_and_visit static asserting: 'The provided Callable must be invocable with value_type const&' [Closed]

> Username: jonesmz  
> Created at: 2025-04-01 20:13:39 UTC  
> Updated at: 2025-04-21 15:27:42 UTC  
> Closed at: 2025-04-04 14:59:42 UTC  
> Url: https://github.com/boostorg/unordered/issues/308  

The documentation for this function here: https://www.boost.org/doc/libs/1_87_0/libs/unordered/doc/html/unordered.html#concurrent_flat_map_emplace_and_cvisit  
  
says that the function must take a reference as it's argument, and such reference is const iff emplace_and_cvisit is used.  
  
But the code for emplace_and_visit  
  
concurrent_node_set  
```  
      template <class Arg1, class Arg2, class... Args>  
      BOOST_FORCEINLINE bool emplace_and_visit(  
        Arg1&& arg1, Arg2&& arg2, Args&&... args)  
      {  
        BOOST_UNORDERED_STATIC_ASSERT_PENULTIMATE_ARG_CONST_INVOCABLE(  
          Arg1, Arg2, Args...)  
        BOOST_UNORDERED_STATIC_ASSERT_LAST_ARG_CONST_INVOCABLE(Arg2, Args...)  
        return table_.emplace_and_visit(  
          std::forward<Arg1>(arg1), std::forward<Arg2>(arg2),  
          std::forward<Args>(args)...);  
      }  
```  
  
concurrent_flat_set  
```  
      template <class Arg1, class Arg2, class... Args>  
      BOOST_FORCEINLINE bool emplace_and_visit(  
        Arg1&& arg1, Arg2&& arg2, Args&&... args)  
      {  
        BOOST_UNORDERED_STATIC_ASSERT_PENULTIMATE_ARG_CONST_INVOCABLE(  
          Arg1, Arg2, Args...)  
        BOOST_UNORDERED_STATIC_ASSERT_LAST_ARG_CONST_INVOCABLE(Arg2, Args...)  
        return table_.emplace_and_visit(  
          std::forward<Arg1>(arg1), std::forward<Arg2>(arg2),  
          std::forward<Args>(args)...);  
      }  
```  
  
is static asserting that the function must be callable with a const&.  
  
Assuming I am reading the documentation correctly, my intention for using `emplace_and_visit` was to forward a set of constructor arguments into the `emplace_and_visit` function, and then have the same callback for both the "It was already present", and "it was newly created" arguments, which modify the held item to increase a reference count member variable.  
  
If the value was newly created, that increases the reference count from 0 to 1, and if it was already present, it increases from N to N+1.  
  
Later, if the refcount is dropped to zero by a call to concurrent_node_set::erase_if(), that item would be removed.

---

## Comment 1

> Username: jonesmz  
> Created at: 2025-04-03 05:17:01 UTC  
> Url: https://github.com/boostorg/unordered/issues/308#issuecomment-2774518565  

Similarly, `erase_if` has nothing in the documentation indicating that the predicate must be non-mutating (for the two-argument form of `erase_if` if that matters) other than this code example  
  
```  
m.erase_if([](auto& x) {  
  return x.second == 0; // erase the elements whose mapped value is zero  
});  
```  
which shows a non-const reference being used for the whole table visitation: https://www.boost.org/doc/libs/1_87_0/libs/unordered/doc/html/unordered.html#concurrent_whole_table_visitation

---

## Comment 2

> Username: cmazakas  
> Created at: 2025-04-03 19:58:43 UTC  
> Url: https://github.com/boostorg/unordered/issues/308#issuecomment-2776790765  

Did you have any code in mind that currently didn't compile?  
  
For what it's worth, we can't generally let callers mutate a key which is why sets are const-only. Because we'll have already constructed the key and located it in the map and permitting a change of that would break invariants.  
  
On CE, you can see that maps permit mutation of the corresponding value but not the key as well: https://godbolt.org/z/sP5PM67E3

---

## Comment 3

> Username: jonesmz  
> Created at: 2025-04-03 22:26:26 UTC  
> Url: https://github.com/boostorg/unordered/issues/308#issuecomment-2777130605  

I lay out my use case here: https://github.com/boostorg/unordered/issues/244  
  
Sadly, i can't share copy-paste, as it's a proprietary codebase, but the linked issue shows all the important bits.  
  
My intention is to use concurrent_node_set, as i need the address stability, and working with a held std::unqiue_ptr is quite cumbersome and requires a lot of extra hoops.  
  
But fundementally i'm not going to mutate the member variable of the item held in the set that's used by the equality comparison or hash, just a refcount.  
  
I suppose i could mark the refcount mutable and not worry about it, but the documentation strongly implies i can emplace_and_visit with the visitors mutating the item, and erase_if doesn't say one way or another.

---

## Comment 4

> Username: joaquintides  
> Created at: 2025-04-04 10:36:45 UTC  
> Updated at: 2025-04-04 10:44:36 UTC  
> Url: https://github.com/boostorg/unordered/issues/308#issuecomment-2778274346  

Hi Michael,  
  
For concurrent **maps**, you can indeed use non-const reference accepting functions in `emplace_and_visit`. But for concurrent **sets** the reference must be const as stated [here](https://www.boost.org/doc/libs/1_87_0/libs/unordered/doc/html/unordered.html#concurrent_flat_set_emplace_and_cvisit): (bold mine):  
  
> Inserts an object, constructed with the arguments `args`, in the table if there is no element in the table with an equivalent key, and then invokes `f1` with a **const reference** to the newly created element. Otherwise, invokes `f2` with a **const reference** to the equivalent element.  
  
The rationale for that is that you can't freely change the key within the visitation functions (it would break the set's invariants). You say you promise not to do it, but the lib can't possibly know that.  
  
Your best option is, as you indicate, to make the refcount mutable.  
  
As for `erase_if`, you're right that the docs don't state whether the reference is const or not (it is const for sets, non-const for maps). We'll fix this.

---

## Comment 5

> Username: jonesmz  
> Created at: 2025-04-04 14:45:07 UTC  
> Url: https://github.com/boostorg/unordered/issues/308#issuecomment-2778944354  

I recommend removing the functions which are named as if they are the non-const version from the api of the containers which require const.

---

## Comment 6

> Username: joaquintides  
> Created at: 2025-04-06 15:50:52 UTC  
> Updated at: 2025-04-06 15:52:11 UTC  
> Url: https://github.com/boostorg/unordered/issues/308#issuecomment-2781486239  

> I recommend removing the functions which are named as if they are the non-const version from the api of the containers which require const.  
  
`*_cvisit` and `*_visit` functions are actually different, even if they both feed a const reference to the visitation function in the case of sets. In the [Concurrency Requirements and Guarantees section](https://www.boost.org/doc/libs/1_87_0/libs/unordered/doc/html/unordered.html#concurrent_node_set_concurrency_requirements_and_guarantees) for `concurrent_node_set` (it's the same for `concurrent_flat_set`), it is stated that:  
  
> When executed internally by a `boost::concurrent_flat_set`, the following operations by a user-provided visitation function on the element passed do not introduce data races:  
> [...]  
> * Mutable modification of the element:  
>   * [...]  
>   * Within a non-const container function whose name does not contain `cvisit`, for the last (or only) visitation function.  
  
What this means is that, for instance, `emplace_and_visit(..., f1, f2)` executes `f2`  in a synchronized way (guarded by a mutex), whereas `emplace_and_cvisit(..., f1, f2)` executes `f2` concurrently. In your particular case, if you're making your refcount mutable and want to increment it in `f2`, you have two safe options:  
  
* If your refcount is not an atomic variable, you have to use `emplace_and_visit` so that incrementing it does not incur a data race.  
* If you make the refcount atomic, you can use `emplace_and_cvisit` as atomic increment does not need any kind of synchronization.  
  
HTH

---

## Comment 7

> Username: jonesmz  
> Created at: 2025-04-21 15:27:41 UTC  
> Url: https://github.com/boostorg/unordered/issues/308#issuecomment-2818789043  

I'm now using boost::concurrent_node_set, with mutable reference count, in my production environment. Replacing a circa 2010 hand-written hash map implementation with lots of cruft.  
  
Thank you for the excellent open source code.
