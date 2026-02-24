# #233 Fix the box constructor [Closed]

> Username: sdebionne  
> Created at: 2015-02-26 10:58:44 UTC  
> Updated at: 2015-04-09 11:11:29 UTC  
> Closed at: 2015-04-09 11:11:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/233  

Allow the construction of box from different point types than the one used internally by box (this way the use of geometry::convert makes more sense).

---

## Comment 1

> Username: mkaravel  
> Created_at: 2015-02-26 11:39:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/233#issuecomment-76164147  

I am okay with merging this.  
Please update the copyright headers if you want.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2015-02-26 16:06:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/233#issuecomment-76205186  

And I'm not sure about it. This change makes the `model::box` inconsistent. Any other geometry model can't be created from points of arbitrary type. More consistent would be to throw away the calls to `convert()` and initialize points using initialization list, it should also be faster (similar thing could be done for segment since now copy assignment is used...).  
  
Is such ctor taking a pair of arbitrary points needed by you? This is actually more general question, should all geometries (or maybe only the simple ones) be created from geometries of arbitraty types? Doesn't we have `assign()` and `convert()` for this purpose?

---

## Comment 3

> Username: sdebionne  
> Created_at: 2015-02-26 17:07:15 UTC  
> Updated_at: 2015-02-26 17:08:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/233#issuecomment-76219258  

I did not realize that it would make box inconsistent. The way I interpreted the documentation is that the constructor of box takes two models of `Point` as argument, not the exact point type used internally by box, so I was a bit surprised to see that it would not compile. Moreover the call to `convert()` was already present.  
  
I found out this 'issue' while trying to construct a `box<model::point<...> >` from adapted `boost::array`s. I can not not say if this should be generalized or not, but it's quite handy (and less verbose). For instance:  
  
```  
my_box_t b(p1, p2);  
```  
  
without the arbitrary points constructor would be written:  
  
```  
typedef point_type<my_box_t> point_t;  
point_t p1b, p2b;  
  
convert(p1b, p1);  
convert(p2b, p2);  
  
my_box_t b(p1b, p2b);  
```  
  
Actually we could have two versions of the box constructor, one with `Point` (with the call to `convert()` stripped and using initialization list) and one with `Point1` and `Point2` with  the conversion...

---

## Comment 4

> Username: awulkiew  
> Created_at: 2015-02-27 17:16:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/233#issuecomment-76431986  

Indeed, there could be 2 ctors taking a pair of points. The same could probably be done for segment, point and point_xy. Furthermore we should probably only allow the creation if the coordinate system and dimension of arguments was the same as the Point's. We could use MPL_ASSERT or enable_if for this purpose.  
  
Barend what do you think about it?  
Is there a reason why this wasn't done in the past?

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2015-03-11 20:01:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/233#issuecomment-78358736  

Actually I don't know why convert is there in the first place. Just looked, it is once changed (by me) in 2011.  
Two constructors one templated, one not, seems ambiguous to me (does it compile?)  
In segments the points are just assigned.  
For points it is not really applicable I think?   
  
So I'm a bit puzzled why I added convert there. I'm trying running the unit test without it.  
  
So I agree with Adam that we have assign, convert, make for that purpose (there is currently not one supporting your use-case, but that can be added).  
  
We cannot throw away the convert right now because that might be a breaking change for some users (though I don't really think so because the point type is the same - only if there is no assignment operator which would be problematic in any use case with our library). We can change it after 1.58  
  
Thanks for your work and your contributions! Your contributions are welcome, even if we cannot accept them all (I state that because I'm sorry to also close the one about unit testing, both on one day).

---

## Comment 6

> Username: mkaravel  
> Created_at: 2015-03-11 20:27:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/233#issuecomment-78363756  

I think convert is there to account for the fact that points may not be copyable by means of an assignment operator or a copy constructor.  
If this is indeed the reason, then convert is one easy way to do it, and most probably as optimal as direct assignment.

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2015-03-11 21:28:14 UTC  
> Updated_at: 2015-03-11 21:32:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/233#issuecomment-78375792  

Indeed. For example a point defined as int[3] ( <boost/geometry/geometries/adapted/c_array.hpp ) is not copyable. There is a unit test testing that.  
  
Such a point type can be used for e.g. distance calculations, but the support in the library is not broad.  
So we should leave convert there - then we probably should add it to segment too  
  
So that brings us back to the original PR: can we add support for any point type, or is that outside the responsibility of the box class? (I think the last - and create a make... function to mimic a constructor with another type)

---

## Comment 8

> Username: sdebionne  
> Created_at: 2015-03-11 21:28:15 UTC  
> Updated_at: 2015-03-11 21:32:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/233#issuecomment-78375797  

@barendgehrels No worry !  
  
@mkaravel Does that mean that Point could be NonCopyable ? For info, variants require that bounded types are CopyConstructible or MoveConstructible, that adds an other requirement to Geometry if the user want to use the variant support of the lib.  
  
What is the policy about C++11 in Boost.Geometry ? I'm asking because an other solution for my use case would be to have a `return_convert<Geometry>()` (in the same vein than `return_envelope()`) that could be efficiently implemented using move and rvalue reference.  
  
AFAIR, I have already coded this to solve the following issue with `assign()`: when assigning two Linestrings, the destination Linestring is cleared (call to `clear()`) and then resized. But if the destination Linestring is a Range and not a Container that would not compile. I end up reimplementing `assign()` for Point Range  as (pseudo code):  
  
``` c++  
template <typename PointRange, typename OutputIterator>  
void assign(PointRange const& geom, OutputIterator out)  
{  
    boost::transform(geom, out, return_convert<Geometry>())`  
}  
```  
  
Here, the eventual resizing of the destination Geometry is up to the caller.

---

## Comment 9

> Username: barendgehrels  
> Created_at: 2015-03-11 21:33:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/233#issuecomment-78376968  

We support C++03 by default. There is some code by Adam to support C++11 but that is protected by defines

---

## Comment 10

> Username: barendgehrels  
> Created_at: 2015-03-11 21:34:17 UTC  
> Updated_at: 2015-03-11 21:34:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/233#issuecomment-78377222  

return_convert<Geometry>() would have the same purpose as make<Geometry>()

---

## Comment 11

> Username: awulkiew  
> Created_at: 2015-03-12 00:51:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/233#issuecomment-78404704  

@barendgehrels Honestly, I doubt that anyone uses a raw array as a Point. It's more convenient and clear to use model::point which works out of the box, can be stored in standard containers, in general works with the whole library, etc. In many algorithms the copies of Points are created, the algorithms aren't tested for array-Point types and so on.  
So some of the algorithms (cartesian? distance) would work for array-Points and they could be stored in a model::box but AFAIU only this model. I'd vote for dropping the support in model::box. It should be faster if members were initialized using initialization list and if the user desired he could implement his own box type.  
  
Of course it'd still be required in a box ctor taking 2 arbitrary Point types. However I'm not sure if this should be allowed.  
  
@mkaravel So yes, I think that convert() is slower than an assignment operator, and even slower than ctor called on initialization list.  
  
AFAIK in C++11 the rvalue returned by a function should automatically be moved. Furthermore classes without explicitly defined copy or move ctors/assignments should have defined move ctor by default. So point, box, segment should be movable. Also OGC models (linestring, polygon, etc.) should be movable because all member types (std::vector) should be movable in C++11. For now there is no function returning them but this might change I suppose. Anyway, we probably don't need to do anything additional.

---

## Comment 12

> Username: barendgehrels  
> Created_at: 2015-03-12 08:22:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/233#issuecomment-78439673  

@awulkiew I'm OK to drop support for raw C arrays in boxes. You are right that it is not supported in segments, lines, etc. If it stays, for point only, working for distance I'm OK (this was always one of our samples, and that might be useful for anyone because they can pick up tiny parts of our library).

---

## Comment 13

> Username: sdebionne  
> Created_at: 2015-03-12 08:24:27 UTC  
> Updated_at: 2015-03-12 08:30:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/233#issuecomment-78439836  

For the sake of completeness, here is my `return_convert<Geometry>` polymorphic function object:  
  
``` cpp  
template <typename Geometry1>  
struct return_convert  
{  
    template <typename Geometry2>  
    Geometry1&& operator()(Geometry2 const & _geom) const  
    {  
        Geometry1 res;  
        boost::geometry::convert(geom, res);  
        return std::move(res);  
    }  
  
    Geometry1 const& operator()(Geometry1 const & _geom) const  
    {  
        return _geom;  
    }  
};  
```  
  
I don't think it serves the same purpose as `make<Geometry>()`. But it requires Geometry1 to be DefaultConstructible (not all Gometry have such requirement, right ?)

---

## Comment 14

> Username: awulkiew  
> Created_at: 2015-03-12 14:27:25 UTC  
> Updated_at: 2015-03-12 14:28:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/233#issuecomment-78489467  

Indeed, `make` serves a different purpose. I'd rather implement a function and give it a different name:  
  
```  
geometry_cast<Geometry2>(geometry1)  
```  
  
The optimization returning `const&` could be choosen with `enable_if/disable_if`. Actually there could be yet another one, enabled if `is_convertible<Geometry1, Geometry2>::value == true`, though this could be dangerous so would require more thinking.  
  
Instead of by rval ref a Geometry should probably be returned by value (we must support C++98). Besides I think it wouldn't be needed to use rval ref. In C++11 it should be moved anyway. In C++98 RVO should be applied (at least in this case). And trivially-copyable types like Box or Point would still be copied.

---

## Comment 15

> Username: mkaravel  
> Created_at: 2015-03-13 11:25:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/233#issuecomment-78927056  

@sdebionne Yes, points can be non-copyable (so far at least; we will see if this changes).  
  
About variants: I think Bruno in a private email exchange explained it in a very nice way: variants are not geometries. Support for variants in Boost.Geometry does not mean that variants are understood as geometries, but rather than Boost.Geometry is **_variant-aware_** (i.e., it can handle variants).

---

## Comment 16

> Username: awulkiew  
> Created_at: 2015-03-27 22:19:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/233#issuecomment-87106440  

I thought about one more thing regarding this ctor. If the user wanted to initialize the box using the uniform initialization syntax the compiler would be confused, consider:  
  
```  
box<point> b{{0, 0}, {1, 1}};  
```  
  
So the ctor taking two Points is needed. Should we have this additional one taking two arbitrary Points? IMO we could have something like this but the purpose should be different. My version wouldn't call `bg::convert()` but rather take two universal references and forward the parameters into the members. So the purpose would be different. IMO conversion between various Points should be done in function like `convert()`.  
  
We could rather think about a function similar to the one mentioned by Samuel `return_converted` or `geometry_cast`. It should play well with this ctor taking two universal references.

---
