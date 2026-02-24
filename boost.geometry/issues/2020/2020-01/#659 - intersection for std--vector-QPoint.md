# #659 - intersection for std::vector<QPoint*> [Open]

> Username: LurkerPhD  
> Created at: 2020-01-19 13:12:16 UTC  
> Updated at: 2020-01-21 11:22:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/659  

Hi. I'm trying to get intersection for two std::vector<QPoint*>, where QPoing is a home brew point class. Then I got allocation errors for boost declares a QPoint* without instanciation when a temporary QPoint object is needed. I've solved this problem by simply adding  
```  
template <typename point, typename policy>  
struct boost::geometry::robust_point_type<point *, policy> {  
  typedef point type;  
};  
template <typename point>  
struct boost::geometry::robust_point_type<  
    point *, boost::geometry::detail::no_rescale_policy> {  
  typedef point type;  
};  
```  
and it works.  
The thing is, I'm a newbie to c++ and not so secure about my coding. Is there any important reason not to do this in the first place? Like causing more errors in some situation or sth else I should be ware?

---

## Comment 1

> Username: awulkiew  
> Created at: 2020-01-20 21:13:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/659#issuecomment-576431766  

It looks ok, i.e. in core of the library pointers are handled in a similar way. However pointer types are not tested in general so it is possible that in some case you'd get something unexpected.  
  
Have you considered wrapping this pointer in a class and pass an object of this wrapper instead?  
  
Out of curiosity, what is your use case? Why do you need a container of pointers instead of a container of points? A container of points would not give you any problems and I think it would be faster to process as well.

---

## Comment 2

> Username: mloskot  
> Created at: 2020-01-20 21:53:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/659#issuecomment-576440915  

I hope `QPoint` is not a polymorphic type.  
With Boost.Geometry your best bet is to use value types (as defined by Alexander Stepanov), so try to get rid of the pointer as @awulkiew suggested.

---

## Comment 3

> Username: LurkerPhD  
> Created at: 2020-01-21 09:55:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/659#issuecomment-576604075  

> It looks ok, i.e. in core of the library pointers are handled in a similar way. However pointer types are not tested in general so it is possible that in some case you'd get something unexpected.  
>   
> Have you considered wrapping this pointer in a class and pass an object of this wrapper instead?  
>   
> Out of curiosity, what is your use case? Why do you need a container of pointers instead of a container of points? A container of points would not give you any problems and I think it would be faster to process as well.  
  
thx for your tip! I'll consider the wrapper thing. It seems like a promising alternative.  
  
 I'm using boost::geometry in a mesh in which several cells could share a point. The point itself contains constantly changing variables. If I use a container of point as a cell, it seems troublesome to change all the surrouding cells when one point updates. And I need to do some topology operators, something like cut cells into sub-cells (like 2D mesh cut by linestring) so I need the intersection algorithm.   
  
I known boost::geometry aims to GIS and there are other mesh oriented libraries like CGAL. But I do like and need the boost's capability to adapt a legecy class.

---

## Comment 4

> Username: mloskot  
> Created at: 2020-01-21 10:19:12 UTC  
> Updated at: 2020-01-21 10:19:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/659#issuecomment-576613872  

> I known boost::geometry aims to GIS   
  
I don't entirely agree with this statement.  
The documentation is quite clear about how it uses the OGC specs, in https://www.boost.org/libs/geometry/doc/html/geometry/introduction.html it says  
  
> The library follows existing conventions:   
> - conventions and names from one of the OGC standards on geometry (...)  
  
So, the library follows vocabulary and interfaces to model the system of the geometries and name (some of) the algorithms, but it does not mean it is a GIS specific library.

---

## Comment 5

> Username: LurkerPhD  
> Created at: 2020-01-21 11:00:40 UTC  
> Updated at: 2020-01-21 11:06:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/659#issuecomment-576630537  

> > I known boost::geometry aims to GIS  
>   
> I don't entirely agree with this statement.  
> The documentation is quite clear about how it uses the OGC specs, in https://www.boost.org/libs/geometry/doc/html/geometry/introduction.html it says  
>   
> > The library follows existing conventions:  
> >   
> > * conventions and names from one of the OGC standards on geometry (...)  
>   
> So, the library follows vocabulary and interfaces to model the system of the geometries and name (some of) the algorithms, but it does not mean it is a GIS specific library.  
  
Oh sorry for my expression. I don't really mean boost::geometry is a GIS specific library. Otherwise I won't use it at all. I mean maybe some of its built-in data structures or algorithms are more or less more suitable for GIS implication? Maybe, I don't know. I only know GIS from the text books.   
  
I use boost for its generics. But hey, thx for additional knowledge and clarification!

---

## Comment 6

> Username: mloskot  
> Created at: 2020-01-21 11:22:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/659#issuecomment-576638423  

Certainly, there is GIS influence observable in BG (e.g. in terms of what kind of algorithms are offered first, what are missing), but to me it is suitable as general purpose geometry library.
