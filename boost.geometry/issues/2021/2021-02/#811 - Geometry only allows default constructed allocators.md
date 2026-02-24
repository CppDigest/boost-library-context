# #811 - Geometry only allows default constructed allocators [Closed]

> Username: kleunen  
> Created at: 2021-02-16 10:31:32 UTC  
> Updated at: 2021-07-06 17:29:30 UTC  
> Closed at: 2021-07-06 17:29:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/811  

The geometry classes allow overriding the allocators:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/geometries/multi_linestring.hpp#L50-L55  
  
However the constructor gives no possibility to provide a reference to the allocator:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/geometries/multi_linestring.hpp#L68-L76  
  
As can be done with classes in the STL:  
````C++  
explicit vector (const allocator_type& alloc = allocator_type());  
explicit vector (size_type n);          vector (size_type n, const value_type& val,                  const allocator_type& alloc = allocator_type());  
template <class InputIterator>   vector (InputIterator first, InputIterator last,           const allocator_type& alloc = allocator_type());  
vector (const vector& x); vector (const vector& x, const allocator_type& alloc);  
vector (vector&& x); vector (vector&& x, const allocator_type& alloc);  
vector (initializer_list<value_type> il,        const allocator_type& alloc = allocator_type());  
````  
  
So only default constructable allocators can beused.   
  
Can I use non-default constructable allocators somehow ?

---

## Comment 1

> Username: mloskot  
> Created at: 2021-02-16 11:01:57 UTC  
> Updated at: 2021-02-16 11:02:19 UTC  
> Url: https://github.com/boostorg/geometry/issues/811#issuecomment-779762061  

It could be possible to register your own models as geometries (e.g. `std::vector` is a valid model for LineString geometry),  
but, conceptually, you *may* hit requirement of geometries must be default constructible, I can't remember.  
By the way, you may also try custom geometry type registration mode.  
And, a default constructible collection (e.g. `std::vector`) will [require default constructible allocator](https://stackoverflow.com/a/40683807/151641), obviously.

---

## Comment 2

> Username: kleunen  
> Created at: 2021-02-16 12:12:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/811#issuecomment-779797785  

> It could be possible to register your own models as geometries (e.g. `std::vector` is a valid model for LineString geometry),  
> but, conceptually, you _may_ hit requirement of geometries must be default constructible, I can't remember.  
  
Yes, it probably depends on which operations you use, whether they would require constructing a temporary object.   
But I already saw that indeed the geometries are just basicly concepts, so maybe it is an option to implement custom geometries  
  
I was also thinking about creating a custom allocator which has a default constructor and uses a global variable for the constructor. But I am a bit stuck on implementing this custom allocator.   
  
But adding the allocator to the constructor of the geometries would not be a bad idea, i would say.

---

## Comment 3

> Username: kleunen  
> Created at: 2021-02-16 19:02:39 UTC  
> Updated at: 2021-02-16 19:08:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/811#issuecomment-780052191  

> By the way, you may also try custom geometry type registration mode.  
  
Ah yes, that does actually work:  
https://wandbox.org/permlink/RYKtjklQkMbgxvlk  
  
and conversion:  
https://wandbox.org/permlink/wD65zbF0gKYGPS92

---

## Comment 4

> Username: mloskot  
> Created at: 2021-02-16 19:25:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/811#issuecomment-780064310  

Yup, `std::vector` is a perfectly support model of geometry.

---

## Comment 5

> Username: kleunen  
> Created at: 2021-02-16 19:34:39 UTC  
> Updated at: 2021-02-16 19:48:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/811#issuecomment-780069322  

But no register for polygon it seems, only multipolygon:  
https://www.boost.org/doc/libs/1_75_0/libs/geometry/doc/html/geometry/reference/adapted/register.html  
  
but I can define my own class for a polygon I guess.

---

## Comment 6

> Username: mloskot  
> Created at: 2021-02-16 20:54:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/811#issuecomment-780111619  

There are some reasons behind it  
  
https://github.com/boostorg/geometry/blob/b786bd9dd98ce2e72ae9594af1fb2ce8619512e0/example/c06_custom_polygon_example.cpp#L56-L58  
  
Also, trivial polygons without holes can be represented by ring (i.e. custom registered with `BOOST_GEOMETRY_REGISTER_RING`).

---

## Comment 7

> Username: kleunen  
> Created at: 2021-02-20 15:17:58 UTC  
> Url: https://github.com/boostorg/geometry/issues/811#issuecomment-782697293  

After some struggles, i think I have it working now.
