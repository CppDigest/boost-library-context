# #245 Remove misleading overloads of front() and back() in range and fix a missing const in sub_range [Closed]

> Username: sdebionne  
> Created at: 2015-03-06 14:01:46 UTC  
> Updated at: 2015-03-09 15:36:31 UTC  
> Closed at: 2015-03-09 08:18:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/245  

Remove misleading overloads of `front()` and `back()` since the constness of a Range does not mean that the underlying iterator (and so value type) is const (e.g. you can have a const Range of mutable iterators and vice versa). Use `boost::range_reference<>`.  
  
In the same vein, I wonder if `range::resize()`, `range::clear()` and `range::push_back()` should be moved in a new file "util/container.hpp" to make it more obvious that these fonctions require Containers ?

---

## Comment 1

> Username: sdebionne  
> Created_at: 2015-03-06 14:38:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/245#issuecomment-77568378  

Now that I think of it, removing the overloads may break codes that use Containers as Ranges...  
  
A possible solution to the problem of constness (Containers have deep constness, so iterators of const containers are const_iterators while Ranges behave more like pointers) could be to internally convert user inputs to Ranges so that anywhere in Boost.Geometry code Ranges could assumed.  
  
There are other places where the issue may arise, see for instance:   
  
```  
template <typename Polygon>  
struct ring_return_type<polygon_tag, Polygon>  
{  
    typedef typename boost::remove_const<Polygon>::type nc_polygon_type;  
  
    typedef typename mpl::if_  
        <  
            boost::is_const<Polygon>,  
            typename traits::ring_const_type<nc_polygon_type>::type,  
            typename traits::ring_mutable_type<nc_polygon_type>::type  
        >::type type;  
};  
```  
  
Here, the constness of the ring type is based on the constness of the Polygon template parameter (and so is assuming a Container). But a Polygon could be implemented as a Range of Rings (with two special accessors `outer()` and `inners()`).  
  
Hoping it's not too confusing...

---

## Comment 2

> Username: awulkiew  
> Created_at: 2015-03-06 15:55:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/245#issuecomment-77581742  

Thanks for the PR!  
Indeed range_reference<> should be used.  
  
In Boost.Geometry the algorithms take const Geometries as input parameters and non-const as output. My understanding is that they are Geometries concepts which behave like Ranges, not the other way around. And the information about constness is propagated into the internals of algorithms. I'm not sure if messing with this machinery is a good idea. But if this is a problem we certainly should change or tweak it.  
  
Let's consider 2 possibly problematic scenarios.  
- A const Range is passed into an algorithm as mutable Geometry. Such situation is only possible if this Range is an output Geometry. Output geometries are changed by the algorithm, that is an algorithms clears them resizes and adds points to them. So they must be Containers, not Ranges.  
- A mutable Range is passed as input Geometry. The algorithm takes const reference so this range will be passed as const and this constness will be propagated into the lower levels.  
  
Is the purpose of this PR to solve some real problematic issue?  
Btw, you should probably add the tests for it as well.

---

## Comment 3

> Username: mkaravel  
> Created_at: 2015-03-06 15:56:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/245#discussion_r25953978  

I am confused about this change. Why is the `const` required here?

---

## Comment 4

> Username: awulkiew  
> Created_at: 2015-03-06 15:57:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/245#discussion_r25954006  

`front()` taking non-const reference shouldn't be removed. With your change it isn't possible to return a non-const reference if a non-const Container is passed because it'd be passed as const into the function. Consider:  
  
```  
std::vector<int> v(10, 0);  
int & i = range::front(v);  
```

---

## Comment 5

> Username: awulkiew  
> Created_at: 2015-03-06 15:57:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/245#discussion_r25954019  

Same here, this should stay.

---

## Comment 6

> Username: awulkiew  
> Created_at: 2015-03-06 15:58:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/245#discussion_r25954125  

To work properly also with Containers the `Range const` should be passed into the `range_reference<>`.

---

## Comment 7

> Username: awulkiew  
> Created_at: 2015-03-06 15:59:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/245#discussion_r25954147  

Same here `BidirectionalRange const`.

---

## Comment 8

> Username: awulkiew  
> Created_at: 2015-03-06 16:01:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/245#discussion_r25954336  

Why is this a problem? Isn't `ring_type<>` returning a type of rings suitable for the passed Geometry? So const ring for const Geometry and non-const otherwise?

---

## Comment 9

> Username: sdebionne  
> Created_at: 2015-03-06 16:45:36 UTC  
> Updated_at: 2015-03-06 16:46:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/245#issuecomment-77591273  

You are right, the use of `range_reference<>` fix the problem. For Ranges the two overloads are equivalent, but the const version is indeed necessary for Container. Am I right to say that `range::at()` should be fixed as well ?  
Is it OK to update the branch or should I make an other PR ?

---

## Comment 10

> Username: sdebionne  
> Created_at: 2015-03-06 16:54:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/245#discussion_r25958544  

Well in my code `ring_type<>::type` is mutable while range::at() returns a const. My ring type is `iterator_range<const point *>`. Let me investigate futher...

---

## Comment 11

> Username: awulkiew  
> Created_at: 2015-03-06 16:56:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/245#issuecomment-77593377  

Yes, `at()` should be fixed as well. I'd create a new PR to not push commits reverting the changes done in previous commits. You should probably also add some tests. I recently added tests for `boost::iterator_range` and `std::pair` so cases for const pointers could be added there (in test/util/range.cpp).  
  
Does it fix all of the problems noticed by you?  
Or are there some other problematic places?

---

## Comment 12

> Username: sdebionne  
> Created_at: 2015-03-06 17:07:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/245#issuecomment-77595501  

The problem with `sub_range` is still there, but I'm not so sure that it's actullay `sub_range` the problem but may be `ring_type<>` and the way it dispatchs to `ring_const_type<>` and `ring_mutable_type<>`...  
  
I'm going to submit a PR for `range::at()`, `range::front()` and `range::back()`.  
  
And I'll add tests for const pointers as its a pattern I use a lot to adapt (read-only) BLOB geometries to Boost.Geometry.

---

## Comment 13

> Username: awulkiew  
> Created_at: 2015-03-06 17:15:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/245#issuecomment-77596796  

Great, thanks!  
  
As for the `sub_range<>`, indeed the problem may be located in `ring_type<>` or some other metafunction. It'd be helpful to create some minimal failing example, though I'm guessing that it could require more code. Maybe it'd be a good idea to move this discussion to the mailing list?

---

## Comment 14

> Username: sdebionne  
> Created_at: 2015-03-09 08:18:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/245#issuecomment-77814538  

My plan is to submits several smaller PRs, I'm closing this one.

---
