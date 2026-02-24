# #12 - Is reinterpret_cast to layout compatible necessary? [Closed]

> Username: cbeck88  
> Created at: 2017-09-12 19:35:38 UTC  
> Updated at: 2017-10-13 18:29:20 UTC  
> Closed at: 2017-10-13 18:29:19 UTC  
> Url: https://github.com/boostorg/pfr/issues/12  

Hi, I'm the same person as render787, I posted in the "great type loophole" [thread](https://www.reddit.com/r/cpp/comments/6ydxya/the_great_type_loophole/) on reddit. I was recently thinking about `magic_get` again :)  
  
I'm not totally sure if my information about `magic_get` is up to date, I guess there has been a fair amount of development since the cppcon talk.  
  
Anyways, from inspection of code for instance here https://github.com/apolukhin/magic_get/blob/master/include/boost/pfr/detail/cast_to_layout_compatible.hpp#L34  
  
it appears to me that the classic, c++14 version works basically this way:  
  
  1. Given user-defined struct `T`, we find the list of members, using trick with `ubiq`. We determine a `typelist` containing the struct members in sequence. (In C++14 this means the "flattened" members.)  
  2. Given the `typelist` of members, we have a custom tuple type, which is supposed have the same layout as a struct with those members, on all compilers. (Assuming it is standard layout.) We can't use `std::tuple` for this because on some compilers `std::tuple` has members in the reverse order, the standard doesn't require that.  
  3. Then we `reintepret_cast` the user-defined struct `T` to the tuple type, so that we can provide all the goodies for it. This is technically undefined behavior, but in practice it works, and `magic_get` tries to mark the pointers as potentially aliasing in order to prevent strict aliasing optimizations from breaking the code.  
  
It occurred to me recently that potentially, the reinterpret cast to unrelated types could be avoided.  
  
What I propose instead is:  
  
  2.5. Given the std::tuple which is layout compatible, compute a list of "offset" numbers. These numbers should be exactly the same as what would be computed by `offsetof` macro on the user-defined type.  
  
  3. Given a user-defined structure instance, the typelist corresponding to it, and the offset list also, we can implement `std::get`, by taking pointer to structure, casting to `char *`, adding the offset, and `reinterpet_cast` to `T *` where `T` is the type. Then we make `magic_get` work through that impl of `get` instead of by the tuple get against the casted structure.  
  
The advantage of this is that, this method of `reinterpret_cast` conforms to the C++ standard completely and the optimizer isn't allowed to break it. It doesn't involve `reinterpret_cast` of unrelated types -- `char *` is always allowed to alias, and the pointer that we cast to `T*` is indeed a legitimate pointer to `T`. It's discussed also on stack overflow: https://stackoverflow.com/questions/46123867/struct-offsets-and-pointer-safety-in-c  
  
I think this method can also work even if the struct type `T` contains types that are not literal. Because, using the `ubiq` method, you can find the size and align of every member type. So imagine it would look like this:  
  
Given  
  
`typelist <T1, T2, T3>`  
  
in a constexpr function, you can create  
  
`using matching_tuple_t = magic_get::tuple< std::aligned_storage<sizeof(T1), alignof(T1)>, std::aligned_storage<sizeof(T2), alignof(T2)>, std::aligned_storage<sizeof(T3), alignof(T3)> >`  
  
and `matching_tuple_t` can be constructed in a `constexpr` function even if `T1, T2, T3` cannot be. You can construct it on the stack, then take differences of pointer to the tuple vs. pointer to the members in order to compute the correct list of offsets.  
  
If you also use the loophole method for ubiq detection then I think this means that `magic_get` can work with any aggregate initializable, standard layout structure, without need for preregistering types or need for constexpr initializability of types. (I'm not sure though, does the ubiq part also need the type to be constexpr initializable? It would be cool if that restriction can be removed.)  
  
I'm not sure if this post is clear,or if there are problems with my idea, but would you ptentially be interested in a pull request that tries this approach? From my point of view it would be really nice if that reinterpret cast can be removed, because I don't think anything else in `magic_get` is not kosher with the standard AFAIK.

---

## Comment 1

> Username: apolukhin  
> Created at: 2017-09-13 18:51:54 UTC  
> Url: https://github.com/boostorg/pfr/issues/12#issuecomment-329262533  

Hi, nice to meet you!  
  
  
I've already implemented the loophole trick in develop branch. To use it just define `BOOST_PFR_USE_LOOPHOLE` to `1`. I'll enable it by default soon.  
  
About avoiding `reinterpret_cast`s of layout compatible types. I've tried to go that path and failed:  
* offsetof() does not work with tuples, so you can not use it  
* you can not do `&get<1>(std::declval<Tuple>()) - &get<0>(std::declval<Tuple>())` because `std::declval<Tuple>()` must not be used in evaluated contexts  
* `addressof` is constexpr only starting from C++17 - this adds some more troubles.  
* you can not default construct `Tuple` because it's members could not be default constructible  
  
But I did not try hard enough :) You could find out tricks that I've missed. Pull requests are welcomed!

---

## Comment 2

> Username: apolukhin  
> Created at: 2017-10-13 18:29:19 UTC  
> Url: https://github.com/boostorg/pfr/issues/12#issuecomment-336532488  

PR was merged to master. Everything works well. Thanks again!
