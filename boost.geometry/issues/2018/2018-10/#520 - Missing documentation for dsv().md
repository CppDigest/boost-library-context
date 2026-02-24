# #520 - Missing documentation for dsv() [Closed]

> Username: pauljurczak  
> Created at: 2018-10-19 02:03:25 UTC  
> Updated at: 2018-12-03 19:36:58 UTC  
> Closed at: 2018-12-03 19:36:46 UTC  
> Url: https://github.com/boostorg/geometry/issues/520  

Reference Alphabetical Index is missing `dsv` and `boost::geometry::detail`. I'm using them in this code snippet:  
````  
    using namespace boost::geometry;  
    model::polygon<model::d2::point_xy<unsigned char>> poly{{{0, 0}, {0, 5}, {5, 0}, {0, 0}}};  
    std::cout << dsv(poly);  
````

---

## Comment 1

> Username: awulkiew  
> Created at: 2018-11-21 22:11:46 UTC  
> Url: https://github.com/boostorg/geometry/issues/520#issuecomment-440826291  

Code in `boost::geometry::detail` is not documented because these are implementation details. What parts of the library details are you using? I cannot see them in this example.  
  
@barendgehrels Is there a reason why `bg::dsv()` is not documented? I see that it existed since the acceptance of Boost.Geometry. Was it considered to be not finished (and is still now)? Or is it simply an omission?

---

## Comment 2

> Username: mloskot  
> Created at: 2018-11-21 22:21:05 UTC  
> Updated at: 2018-11-21 22:21:47 UTC  
> Url: https://github.com/boostorg/geometry/issues/520#issuecomment-440828095  

AFAIS, it is documented. It is just missing from the Doxygen input list:  
  
https://github.com/boostorg/geometry/blob/2a2e0ac41e873e5ec09796ce6d6d0d0cf8f4c63b/doc/doxy/Doxyfile#L205-L208  
  
I think it's been simply overlooked.  
  
The way documentation is generated was changing couple of times since the review, so possibly one of post-review Doxygen re-reconfiguring lost it (e.g. previously, Doxygen greedily scanned `boost/geometry`, now it takes explicit list).

---

## Comment 3

> Username: barendgehrels  
> Created at: 2018-11-21 22:57:07 UTC  
> Url: https://github.com/boostorg/geometry/issues/520#issuecomment-440839432  

@mloskot @awulkiew Agree, it's probably been overlooked.  
As far as I know it is finished - it is less complex than most of the code we have. The main manipulator is not in the detail namespace so it is meant to be used.

---

## Comment 4

> Username: awulkiew  
> Created at: 2018-11-25 21:54:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/520#issuecomment-441477172  

Actually it seems `dsv()` was never documented and other IOs were documented in 1.54 (see: https://www.boost.org/doc/libs/1_54_0/libs/geometry/doc/html/index.html vs https://www.boost.org/doc/libs/1_53_0/libs/geometry/doc/html/index.html). I haven't looked why DSV was ommited then.  
  
I'm reluctant to document it now because I don't like the interface. Besides the geometry the function takes other parameters like coordinate separators, list separators etc. so if the user wanted to use different ones then the defaults they would have to be passed to all calls of `dsv()`. Furthermore not all parts of the DSV string can be defined, e.g. multi-list or polygon-rings separators are missing. What would be perfect is a way of setting up a user-defined state of a stream with custom manipulators, like:  
  
    os << bg::dsv_coordinate_separator("|");  
    // since now this separator will be used in this stream for all geometries  
    os << bg::dsv(point) << std::endl;  
    os << bg::dsv(polygon) << std::endl;  
  
but this is AFAIK impossible. Or is it?  
  
The most obvious would be to have our own stream type (maybe wrapper) capable of storing states defined by us but I'd prefer to avoid it since this solution has its own limitations, worse IMO than the original interface.  
  
Anyway, my point is that before documenting it I'd prefer to investigate other ways of writing this interface. With that said I don't know when I'd do this and I doubt it'd be anytime soon. Or we could document it now and in the unknown future announce a breaking change. What do you think?

---

## Comment 5

> Username: barendgehrels  
> Created at: 2018-11-26 21:53:41 UTC  
> Url: https://github.com/boostorg/geometry/issues/520#issuecomment-441813246  

Why change it? It exists for 7 years or more. I don't think you can change it, even though it is not documented on the site (internally it is).  
I think we should just leave it as it is, because people might use it already for years. If you want something fancier, and you get it working like that, it has to come with another method name...

---

## Comment 6

> Username: awulkiew  
> Created at: 2018-11-27 01:29:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/520#issuecomment-441863382  

Ok. So it simply has to be documented then.  
  
For the record, one solution I came up with is using a function object instead of a free function. So:  
  
    bg::dsv_definition dsv/*(some_separator1, some_separator2, ...)*/;  
    dsv.coordinate_separator = "|";  
    dsv.list_separator = ";";  
    // etc.  
  
    std::cout << dsv(point) << std::endl;  
    std::cout << dsv(polygon) << std::endl;  
  
However if a user wanted he/she could simply write a new function hiding `bg::dsv()` call inside, passing required separators. Or in fact the above function object. So this is probably not needed in Boost.Geometry.

---

## Comment 7

> Username: mloskot  
> Created at: 2018-11-27 08:40:58 UTC  
> Url: https://github.com/boostorg/geometry/issues/520#issuecomment-441973260  

Perhaps this could be displayed as a use case example in the `dsv` docs.  
  
The `dsv` is an old facility initially used as a `printf`-like 'visualisation' for testing and debugging. It predates the SVG. So, it was implemented as very basic.

---

## Comment 8

> Username: barendgehrels  
> Created at: 2018-11-27 19:42:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/520#issuecomment-442190619  

Well, it should also support geoJson, for example. IIRC.

---

## Comment 9

> Username: awulkiew  
> Created at: 2018-12-03 19:36:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/520#issuecomment-443841308  

Fixed by https://github.com/boostorg/geometry/pull/532
