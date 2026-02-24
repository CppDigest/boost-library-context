# #827 Add for_each_dimension and more. [Merged]

> Username: awulkiew  
> Created at: 2021-03-14 02:48:25 UTC  
> Updated at: 2021-03-31 22:08:21 UTC  
> Merged at: 2021-03-31 22:08:20 UTC  
> Closed at: 2021-03-31 22:08:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/827  

This PR adds the following utilities:  
- `for_each_dimension`, `for_each_index`  
- `all_dimensions_of`, `all_indexes_of`  
- `any_dimension_of`, `any_index_of`  
- `none_dimension_of`, `none_index_of`  
  
The naming is derived ftom STL algorithms.  
  
All of them takes UnaryFunction or UnaryPredicate, e.g. as lambda expression which is called for each dimension of a geometry or index. The algorithms pass variable of `std::integral_constant<std::size_t, Index>` into it.  
  
Note that `std::integral_constant<>` defines convenient `constexpr` conversion and call operators so it can be passed directly as compile-time constant.  
  
A quick example. Let's say we want to implement `dot_product()`. We can do this like that:  
```  
template <typename P1, typename P2>  
auto dot_product(P1 const& p1, P2 const& p2)  
{  
    using calc_t = typename select_coordinate_type<P1, P2>::type;  
    // assert dimension equal  
    calc_t result = 0;  
    for_each_dimension<P1>([&](auto index)  
    {  
        result += calc_t(get<index>(p1)) * calc_t(get<index>(p2));  
    });  
    return result;  
}  
```  
Now let's say we want to check if points are strictly equal:  
```  
template <typename P1, typename P2>  
bool are_points_equal(P1 const& p1, P2 const& p2)  
{  
    // assert dimension equal  
    return all_dimensions_of<P1>([&](auto index)  
    {  
        return get<index>(p1) == get<index>(p2);  
    });  
}  
```  
  
Writing recursive templates iterating over dimensions manually is not required anymore.  
Neither is writing templates taking `Index` in `apply()` for `for_each_coordinate`.  
  
I've used `for_each_dimension` in several places in the library replacing older versions. Please let me know if you think that I should rather leave the older versions intact. The main motivation for this utility was the refactoring of centroid for segments/boxes and strategy for linear geometries.  
  
---  
  
I've noticed that MSVC is able to optimize-out raw recursive templates in very simple synthetic tests better than lambda expression (I haven't noticed this with GCC). I would not expect this in normal code though.  
  
I've done some testing comparing newly implemented utilities with `for_each_coordinate`. See them below.  
  
I think it's safe to replace any call to `for_each_coordinate` as well as any number of arithmetic operations like `add_point()`, `multiply_value()`, etc.  
  
Whether or not to replace raw recursive templates (e.g. point_in_point strategy) is debatable. I haven't tested it thoroughly but I guess that the compiler might choose to optimize the code differently. On the other hand theoretically the outcome should be similar, it would greatly simplify the existing code and it would allow us to replace all O(N) implementations with O(logN) easily.  
  
---  
  
What do you think?

---

## Comment 1

> Username: awulkiew  
> Created_at: 2021-03-14 02:50:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/827#issuecomment-798823322  

If you agree with this addition I'll add a proper test later and divide this one commit into several better described commits.

---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2021-03-17 09:59:13 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/827#pullrequestreview-614114610  

Thanks, looks great!

📁 include/boost/geometry/util/for_each_dimension.hpp

```diff
  35 |+ // A third version I tested using C++17 fold expression was faster in some cases.
  36 |+ //   It depended on optimization level.
  37 |+ // This utility was also slightly faster than for_each_coordinate so I assume the compilation
```

> Username: vissarion  
> Created_at: 2021-03-17 09:58:46 UTC  
> Updated_at: 2021-03-31 21:31:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/827#discussion_r595869648  

This is enough for replacement, I agree. What about running times?

> Username: awulkiew  
> Created_at: 2021-03-17 13:57:40 UTC  
> Updated_at: 2021-03-31 21:31:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/827#discussion_r596042893  

Various compilers seems to be optimizing this differently. I'll do some tests and post them here.

> Username: awulkiew  
> Created_at: 2021-03-19 16:42:33 UTC  
> Updated_at: 2021-03-31 21:31:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/827#discussion_r597825274  

I think I'll remove this comment because this mostly reflects one compiler, it covers only a subset of the results and doesn't describe the overall picture. More results can be found in the comments of this PR.


---

## Comment 3

> Username: barendgehrels  
> Created_at: 2021-03-17 22:23:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/827#issuecomment-801480783  

>   
>   
> Thanks, looks great!  
  
Yes, I agree! I will review it soon.

---

## Comment 4

> Username: awulkiew  
> Created_at: 2021-03-18 01:35:08 UTC  
> Updated_at: 2021-03-18 12:59:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/827#issuecomment-801550532  

I added the other implementations to the file so you know what I'm refering to. So right now we have:  
1. The base `for_each_coordinate` O(N),  
2. `for_each_dimension` O(N),  
3. `for_each_dimension` O(logN) version 1 where subranges are defined as absolute indexes in struct tparams,  
4. `for_each_dimension` O(logN) version 2 where subranges are defined as relative indexes in struct params and offset as function tparam,  
5. `for_each_dimension` implemented with C++17 fold expression.  
  
I've done 4 to allow the compiler to instantiate smaller number of structs. It seems that this indeed decreases the compilation time for higher dimensions. But smaller number of template parameters may also be the cause.  
  
----  
  
The testing program calculates some linear algebra operations many times. In *develop* they are implemented with `for_each_coordinate` and in my branch with `for_each_dimension`. Then the coordinates are accumulated and outputted later in order to prevent the compiler to optimize away the algorithm.  
```  
point p = p1;  
bg::add_point(p, p2);  
bg::add_value(p, z);  
bg::multiply_value(p, o);  
bg::detail::for_each_dimension<point>([&](auto index) { dummy += bg::get<index>(p); });  
```  
with the last line replaced by the following when `for_each_coordinate` is tested:  
```  
struct accumulate  
{  
    accumulate(double & d) : result(d) {}  
  
    template <typename Point, size_t Index>  
    void apply(Point const& p)  
    {  
        result += bg::get<Index>(p);  
    }  
  
private:  
    double & result;  
};  
  
// ...  
  
bg::for_each_coordinate(p, accumulate(dummy));  
```  
  
----  
  
Run times with msvc-14.1 Release  
| dimension | fe_coord N | fe_dim N | fe_dim logN v1 | fe_dim logN v2 | fe_dim 17FE |  
| ---------- | ----------- | --------- | --------------- | ---------------- | ------------ |  
| 2     | 1,895 | 1,742 | 1,736 | 1,739 | n/a |  
| 3     | 2,616 | 2,610 | 2,605 | 2,671 | n/a |  
| 4     | 3,471 | 3,494 | 3,467 | 3,561 | n/a |  
| 5     | 4,353 | 4,358 | 4,341 | 4,453 | n/a |  
| 10   | 8,707 | 8,701 | 8,668 | 8,904 | n/a |  
| 100 | 146,952 | 180,973 | 262,123 | 262,963 | n/a |  
  
Run times with gcc-6.3.0 -O2  
| dimension | fe_coord N | fe_dim N | fe_dim logN v1 | fe_dim logN v2 | fe_dim 17FE |  
| ---------- | ----------- | --------- | --------------- | ---------------- | ------------ |  
| 2     | 2,616 | 2,625 | 2,638 | 2,662 | 2,616 |  
| 3     | 3,280 | 3,285 | 3,374 | 3,367 | 3,295 |  
| 4     | 4,012 | 3,959 | 3,949 | 4,021 | 4,035 |  
| 5     | 4,596 | 4,656 | 4,694 | 4,761 | 4,624 |  
| 10   | 7,927 | 6,573 | 6,600 | 6,696 | 6,563 |  
| 100 | 65,036 | 191,959 | 200,202 | 201,398 | 201,303 |  
  
Compilation times with msvc-14.1 Release  
| dimension | fe_coord N | fe_dim N | fe_dim logN v1 | fe_dim logN v2 | fe_dim 17FE |  
| ---------- | ----------- | --------- | --------------- | ---------------- | ------------ |  
| 5     |  2.41 | 2.53 | 2.52 | 2.42 | n/a |  
| 10   |  2.61 | 2.61 | 2.57 | 2.55 | n/a |  
| 100 |  4.41 | 5.86 | 4.13 | 4.05 | n/a |  
  
Compilation times with gcc-6.3.0 -O2  
| dimension | fe_coord N | fe_dim N | fe_dim logN v1 | fe_dim logN v2 | fe_dim 17FE |  
| ---------- | ----------- | --------- | --------------- | ---------------- | ------------ |  
| 5     |  2.15 | 2.17 | 2.15 | 2.18 | 2.15 |  
| 10   |  2.20 | 2.18 | 2.21 | 2.18 | 2.21 |  
| 100 |  2.64 | 2.63 | 2.79 | 2.62 | 2.53 |  
  
I ommited the dimensions < 5 in compilation times because they were all the same.  
  
The run time results for smaller dimensions are comparable. Interesting things start to happen somewhere above dimension 10.  
  
For higher dimensions, it seems that depending on dimension the compiler optimizes `for_each_dimension` better or worse. The gcc results for dimension 100 are particularly interesting. It seems that `for_each_coordinate` is optimized better/differently and that the code generated for logN versions corresponds to the one generated for fold expressions.  
  
It's also interesting that with gcc for dimension 10 `for_each_coordinate` seems to be slower but then for dimension 100 it's faster. So something must be happening between. However I think it's not worth investigating because in general we deal with smaller dimensions.  
  
I also tried to use `BOOST_FORCEINLINE` which resulted in the same run times but greately increased compilation times so it's not worth it.  
  
Have in mind that this is a synthetic test case testing the ability to optimize these particular implementations. In real code these utilities will be surrounded by other code. The compiler may choose to optimize it differently. There will also be other issues affecting the run time more than these algorithms, e.g. time consuming functions like `sin` or `sqrt`, memory access patterns, cache misses, etc. E.g. when I add one `sqrt` call at the end the run time increases 5x for dimension 2 with msvc.  
  
----  
  
Last but not least, with `for_each_dimension` it's a lot easier to rewrite the algorithm to one line, avoiding multiple arithmetic function calls entirely:  
```  
bg::detail::for_each_dimension<point>([&](auto index)  
{  
    dummy += (bg::get<index>(p1) + bg::get<index>(p2) + z) * o;  
});  
```  
Then for smaller dimensions the run times are still comparable but for dimension 100 with msvc-14.1 the run time is 97.090 and with gcc-6.3.0 is 65.412.  
  
It is also possible to do this with `for_each_coordinate` but it requires writing something like this:  
```  
template <typename Point>  
struct operation  
{  
    operation(double & result_, Point const& p2_, double const& z_, double const& o_)  
        : result(result_), p2(p2_), z(z_), o(o_)  
    {}  
  
    template <typename Pt, size_t Index>  
    void apply(Pt const& p1)  
    {  
        result += (bg::get<Index>(p1) + bg::get<Index>(p2) + z) * o;  
    }  
  
private:  
    double & result;  
    Point const& p2;  
    double const& z; // reference because we'd do this for arbitrary, user-defined types  
    double const& o;  
};  
  
// ...  
  
bg::for_each_coordinate(p1, operation<point>(dummy, p2, z, o));  
```  
which is as lot less readable and maintainable.

---

## Comment 5

> Username: awulkiew  
> Created_at: 2021-03-18 19:56:39 UTC  
> Updated_at: 2021-03-18 20:22:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/827#issuecomment-802246050  

More results from a different machine with clang-10.  
  
Run times with clang-10 -O2  
| dimension | fe_coord N | fe_dim N | fe_dim logN v1 | fe_dim logN v2 | fe_dim 17FE |  
| ---------- | ----------- | --------- | --------------- | ---------------- | ------------ |  
| 2   | 1.834   | 1.833   | 1.834  | 1.833  | 1.834   |   
| 3   | 2.748   | 2.747   | 2.748  | 2.751  | 2.745   |  
| 4   | 3.661   | 3.662   | 3.661  | 3.660  | 3.661   |  
| 5   | 4.577   | 4.578   | 4.575  | 4.574  | 4.574   |  
| 10  | 9.145   | 9.145   | 9.144  | 9.146  | 9.142   |  
| 100 | 192.304 | 252.905 | 91.296 | 91.303 | 253.992 |  
  
Compilation times with clang-10 -O2  
| dimension | fe_coord N | fe_dim N | fe_dim logN v1 | fe_dim logN v2 | fe_dim 17FE |  
| ---------- | ----------- | --------- | --------------- | ---------------- | ------------ |  
| 2   | 4.085 | 4.074 | 4.074 | 4.063 | 4.355 |  
| 3   | 4.089 | 4.085 | 4.093 | 4.082 | 4.355 |  
| 4   | 4.112 | 4.091 | 4.113 | 4.091 | 4.368 |  
| 5   | 4.366 | 4.113 | 4.139 | 4.100 | 4.379 |  
| 10  | 4.178 | 4.168 | 4.180 | 4.175 | 4.425 |  
| 100 | 6.033 | 5.766 | 5.424 | 5.103 | 5.266 |  
  
Again, up to dimension 10 (at least) the times are similar.  
  
The run time results for dimension 100 suggest that with clang the fold expressions corresponds to the linear implementation. Whether or not this is the case I don't know.  
  
I'm starting to lean towards logN v2 because the times are the same for smaller dimensions. For higher dimensions it depends on the compiler and probably actual dimension. What is certain however is that the recursion depth is logN as opposed to N. So it is possible to compile the code without tweaking the compiler defaults. But I don't have a strong opinion. What do you think?

---

## Comment 6

> Username: awulkiew  
> Created_at: 2021-03-18 20:20:21 UTC  
> Updated_at: 2021-03-19 00:00:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/827#issuecomment-802262031  

More results, this time with gcc-9.3.0  
  
Run times with gcc-9.3.0 -O2  
| dimension | fe_coord N | fe_dim N | fe_dim logN v1 | fe_dim logN v2 | fe_dim 17FE |  
| ---------- | ----------- | --------- | --------------- | ---------------- | ------------ |  
| 100 | 91.893 | 91.909 | 92.020 | 91.941 | 263.381 |  
  
Compilation times with gcc-9.3.0 -O2  
| dimension | fe_coord N | fe_dim N | fe_dim logN v1 | fe_dim logN v2 | fe_dim 17FE |  
| ---------- | ----------- | --------- | --------------- | ---------------- | ------------ |  
| 100 | 5.072 | 5.082 | 5.193 | 4.990 | 5.004 |  
  
I don't know if there is any pattern. It probably only depends on how a compiler chooses to optimize the code.

---

## Review 7 [Approved]

> Username: barendgehrels  
> Created_at: 2021-03-24 12:54:46 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/827#pullrequestreview-619673907  

Thanks a lot, a big improvement

📁 include/boost/geometry/algorithms/centroid.hpp

```diff
 134 |+             coordinate_type const two = 2;
 135 |+             set<dimension>(centroid, (c1 + c2) / two);
 136 |+         });
```

> Username: barendgehrels  
> Created_at: 2021-03-24 12:47:05 UTC  
> Updated_at: 2021-03-31 21:31:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/827#discussion_r600444157  

Really cool

> Username: awulkiew  
> Created_at: 2021-03-25 02:22:44 UTC  
> Updated_at: 2021-03-31 21:31:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/827#discussion_r600996422  

I agree. :)


📁 include/boost/geometry/util/for_each_dimension.hpp

```diff
 233 |+ 
 234 |+ template <std::size_t N>
 235 |+ struct all_indexes_of_impl2
```

> Username: barendgehrels  
> Created_at: 2021-03-24 12:53:14 UTC  
> Updated_at: 2021-03-31 21:31:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/827#discussion_r600448604  

Maybe you can split this file into the various algorithms?

> Username: awulkiew  
> Created_at: 2021-03-24 19:30:31 UTC  
> Updated_at: 2021-03-31 21:31:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/827#discussion_r600815808  

There are 8 algorithms there so technically they could be divided into 2, 4 or 8 files. The corresponding STL algorithms are defined in `<algorithm>`. So what do you think about renaming the file to `util/algorithm.hpp` and keeping them together?  
  
Btw, what do you think about the namespace? Currently they are in `bg::detail` which gives us the most flexibility. But technically they could be moved to `bg::util` or simply `bg`.

> Username: awulkiew  
> Created_at: 2021-03-31 17:45:33 UTC  
> Updated_at: 2021-03-31 21:31:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/827#discussion_r605099206  

@barendgehrels Would you be ok with keeping them in the same file but to change the file name?

> Username: barendgehrels  
> Created_at: 2021-03-31 18:18:55 UTC  
> Updated_at: 2021-03-31 21:31:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/827#discussion_r605121447  

Sorry, I missed your answer.  
Right, it makes sense, you can keep them then and change the filename.  
I think they're useful also for end users, so if you think they are in final form then `bg` is fine for me. If there is any doubt then I would indeed put them for now in `bg::detail`

> Username: awulkiew  
> Created_at: 2021-03-31 21:38:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/827#discussion_r605237769  

Done. I kept it in the `detail` because it allows us to change it more easily. We can always move it if a user asks for it.


📁 include/boost/geometry/algorithms/detail/assign_indexed_point.hpp

```diff
  54 |-             Geometry, Point, Index, 0, dimension<Geometry>::type::value
  55 |-         >::apply(point, geometry);
  60 |+     detail::for_each_dimension<Geometry>([&](auto dimension){
```

> Username: barendgehrels  
> Created_at: 2021-03-24 12:54:10 UTC  
> Updated_at: 2021-03-31 21:31:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/827#discussion_r600449258  

In almost all code you have the curly brace on next line in these lambda's

> Username: awulkiew  
> Created_at: 2021-03-25 02:27:47 UTC  
> Updated_at: 2021-03-31 21:31:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/827#discussion_r600998088  

Done.


---
