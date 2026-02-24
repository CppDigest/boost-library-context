# #519 - Formatting problem with model::d2::point_xy<unsigned char> [Open]

> Username: pauljurczak  
> Created at: 2018-10-19 01:51:02 UTC  
> Updated at: 2018-11-29 22:56:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/519  

This snippet  
````  
    using namespace boost::geometry;  
    model::polygon<model::d2::point_xy<unsigned char>> poly{{{0, 0}, {0, 5}, {5, 0}, {0, 0}}};  
    std::cout << dsv(poly);  
````  
will produce a bunch of funny characters instead of numerical values:  
````  
((( ,  ), ( , ♣), (♣,  ), ( ,  )))  
````

---

## Comment 1

> Username: awulkiew  
> Created at: 2018-11-15 02:11:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/519#issuecomment-438890235  

Thanks. I think it should be fixed even though I'd discourage from using `unsigned char` as coordinate type since it probably works only in some specific cases.

---

## Comment 2

> Username: awulkiew  
> Created at: 2018-11-27 01:13:44 UTC  
> Url: https://github.com/boostorg/geometry/issues/519#issuecomment-441860300  

After giving it more thought I'm no longer sure if type `unsigned char` should be supported here. The reason is that in order to be consistent we should treat all character types in a specific way, this includes: `char`, `signed char`, `unsigned char`, `wchar_t`, `signed wchar_t`, `unsigned wchar_t`, `char8_t`, `char16_t`, `char32_t` and possibly other types added in the future. Furthermore these types should be supported in all IOs, not only `dsv()`. All of this for types which are not intended to be used as coordinate types and most probably not used in practice by most of the users.  
  
@pauljurczak could you explain why do you use `unsigned char` and give a reason why should it be supported here?  
  
@barendgehrels @mloskot what do you think about it?

---

## Comment 3

> Username: mloskot  
> Created at: 2018-11-27 09:12:07 UTC  
> Url: https://github.com/boostorg/geometry/issues/519#issuecomment-441982800  

I think there are two issues:  
1. `model::d2::point_xy<unsigned char>` - is `unsigned char` a valid type for coordinate?  
2. `dsv` - does it print coordinates as a number, even if represented with C++ character types?  
  
I think, answer to 1. is Yes and answer to 2. is No.   
  
What about promoting the coordinate to numeric type to ensure we avoid the character semantic, here?  
  
https://github.com/boostorg/geometry/blob/d3e572ede341191df9a3b751771ace9c3e554364/include/boost/geometry/io/dsv/write.hpp#L81-L82

---

## Comment 4

> Username: awulkiew  
> Created at: 2018-11-27 13:30:31 UTC  
> Url: https://github.com/boostorg/geometry/issues/519#issuecomment-442058350  

> I think, answer to 1. is Yes  
  
Can you think about some use case for that? A reason why one may want to represent coordinates like that?  
  
> What about promoting the coordinate to numeric type  
  
As I mentioned this won't be enough. You have to handle all other character types promoting to various types accordingly and do this not only this one place but also in the indexed version (for boxes and segments). And it should be done for all IOs consistently so also for `wkt` and `svg`. This is not a problem but adds more complexity to the code with intention to support a use-case which is extremely rare.

---

## Comment 5

> Username: mloskot  
> Created at: 2018-11-27 14:03:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/519#issuecomment-442068920  

> >  I think, answer to 1. is Yes  
> Can you think about some use case for that?  
  
No real problem that would require `unsigned char` for a coordinate type comes to mind. However, the `point_xy` is a template, so its users are free to stick any integer they desire.  
If that is not true, there should be at least `static_assert` blocking such types, plus docs update.  
  
  
> >  What about promoting the coordinate to numeric type  
> As I mentioned this won't be enough. (...)  
  
TBH, I'd just go for no-brainer explicit cast any signed integer to to 64-bit signed; any unsigned integer to 64-bit unsigned and voila ;-)

---

## Comment 6

> Username: awulkiew  
> Created at: 2018-11-27 14:16:58 UTC  
> Url: https://github.com/boostorg/geometry/issues/519#issuecomment-442073405  

> However, the `point_xy` is a template (...)  
  
Right, for completeness.  
  
But how about users who store coordinates in character types because they want to print them as characters for artistic purposes? ;)  
  
> I'd just go for no-brainer explicit cast any signed integer to to 64-bit signed (...)  
  
Isn't printing of greater type slower? I don't like the idea of adding overhead for a typical user only because of some extremely rare situation. With that said a typical user probably uses floating point types anyway. But even in this case we're adding 1 or 2 levels to the call tree decreasing the probability of inlining the code at higher level (possibly?). Not that we have care about it while writing tons of templates anyway.

---

## Comment 7

> Username: mloskot  
> Created at: 2018-11-27 14:29:40 UTC  
> Url: https://github.com/boostorg/geometry/issues/519#issuecomment-442077867  

> But how about users who store coordinates in character types because they want to print them as characters for artistic purposes? ;)  
  
AFAIU, `coordinate_type` is supposed to be a number, by type and by semantic. If someone desires to overload its meaning as a splash of paint...bad luck :)  
  
> Isn't printing of greater type slower?  
  
Even if it is, as per my https://github.com/boostorg/geometry/issues/520#issuecomment-441973260, I doubt performance was ever a concern of `dsv`.

---

## Comment 8

> Username: awulkiew  
> Created at: 2018-11-27 14:51:37 UTC  
> Updated at: 2018-11-27 14:51:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/519#issuecomment-442085804  

> AFAIU, coordinate_type is supposed to be a number, by type and by semantic. If someone desires to overload its meaning as a splash of paint...bad luck :)  
  
Everything is a number here, even `bool` (which coincidentally would be printed as a number). Semantically `char` is a character. Isn't it that if a user stores coordinates as letters and symbols he should get these symbols printed? Who are we to interfere with artist's desires? ;)

---

## Comment 9

> Username: mloskot  
> Created at: 2018-11-27 15:13:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/519#issuecomment-442094116  

I'd rather stick to domain of `T` for `coordinate_type<T>` and, AFAIU, there is no notion of letter, symbol or printability defined for `T`. AFAIU, regardless what C++ animal one wishes to stick in for `T`, its primary role is to be (and act as) a number. Therefore, `dsv` expects numbers.  
  
However, likely, I have not got the sarcasm :)

---

## Comment 10

> Username: awulkiew  
> Created at: 2018-11-27 18:52:22 UTC  
> Url: https://github.com/boostorg/geometry/issues/519#issuecomment-442173883  

I wonder what @barendgehrels thinks about it?

---

## Comment 11

> Username: barendgehrels  
> Created at: 2018-11-27 19:41:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/519#issuecomment-442190315  

As far as I know, streaming wkt or svg for an unsigned char would do the same. And that is fine. If that is the behaviour of streaming unsigned char, the user has to live with it (or select another type). We should not make exceptions for some types. Functionality is very limited anyway (think of an intersection...)  
  
User defined type (ttmath) is also streamed as it is, and that happens to be very precise with lots of decimals.

---

## Comment 12

> Username: awulkiew  
> Created at: 2018-11-27 20:23:46 UTC  
> Url: https://github.com/boostorg/geometry/issues/519#issuecomment-442204006  

@pauljurczak what are you using `unsigned char` for?

---

## Comment 13

> Username: pauljurczak  
> Created at: 2018-11-28 02:50:24 UTC  
> Updated at: 2018-11-28 02:50:45 UTC  
> Url: https://github.com/boostorg/geometry/issues/519#issuecomment-442299456  

> could you explain why do you use unsigned char and give a reason why should it be supported here?  
  
Some geometry problems can be represented on a small discrete grid, e.g. 256 x 256. A specific case I was playing with is [Quick, Draw! Dataset](https://github.com/googlecreativelab/quickdraw-dataset), where 256 x 256 representation is sufficient and may be necessary due to the huge size of dataset.

---

## Comment 14

> Username: awulkiew  
> Created at: 2018-11-29 18:15:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/519#issuecomment-442937272  

@pauljurczak That's interesting. Were you calling some Boost.Geometry algorithms for these geometries? I'm asking because internally while performing calculation we not necessarily promote the coordinate type to something bigger so I'd expect underflows and overflows with `unsigned char`. Unless you explicitly use strategies with `CalculationType` set to something bigger. I'm simply wondering how are you using Boost.Geometry and if you experience other problems.  
  
FYI, if you wanted to perform some more complex operation with Boost.Geometry on geometry internally using `unsigned char` as `CoordinateType` and experienced any problems you could write your own Point type, internally storing data however you like and specialize traits like the ones specialized for `bg::model::point_xy`  
(https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/geometries/point_xy.hpp#L87)  
defining `coordinate_type` as e.g. `int` or `float`  
(https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/geometries/point_xy.hpp#L99)  
and converting from and to `unsigned char` in `access` possibly truncating coordinates outside valid range  
(https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/geometries/point_xy.hpp#L114).  
  
With that said the most simple workaround would obviously be to store data in one way and then convert from this representation to Boost.Geometry types using ints or floats as Coordinate Type for some operations and then back to your storage format.

---

## Comment 15

> Username: pauljurczak  
> Created at: 2018-11-29 22:56:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/519#issuecomment-443025047  

@awulkiew I ended up reusing and writing my own code for this experiment. Boost.Geometry was just one of options I quickly evaluated for this task.  
  
You are right that unless written specifically for this purpose, most libraries would return erroneous results if all calculations were performed in such low precision.
