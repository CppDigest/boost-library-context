# #122 - Clean up the names of types and constructor functions [Closed]

> Username: ldionne  
> Created at: 2015-06-15 21:20:38 UTC  
> Updated at: 2015-09-03 09:39:52 UTC  
> Closed at: 2015-09-02 17:02:01 UTC  
> Url: https://github.com/boostorg/hana/issues/122  

For example, we can currently create a Range by:  
- `boost::hana::make_range(...)`  
- `boost::hana::make<boost::hana::Range>(...)`  
- `boost::hana::range(...)`  
- `boost::hana::range_c<...>`  
  
Furthermore, the type of these objects is some `boost::hana::_range<...>` type, which is implementation-defined. This could be cleaned up to follow a better convention.  
  
Also, the type of `_tuple<...>` is not implementation-defined, but it has a leading underscore. That is **ugly**.  
  
Ideas:  
- All the implementation-defined types go into the `detail` namespace, so there's no mistake with that.  
- Instead of using the word unspecified-type, we should use implementation-defined. Everybody understand that.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-07-28 18:22:28 UTC  
> Url: https://github.com/boostorg/hana/issues/122#issuecomment-125702548  

Thoughts:  
  
``` c++  
tuple<> // proper documentation  
pair<> // proper documentation  
  
set<> // implementation defined type  
map<> // implementation defined type  
range<> // implementation defined type  
```

---

## Comment 2

> Username: viboes  
> Created at: 2015-09-03 06:19:05 UTC  
> Url: https://github.com/boostorg/hana/issues/122#issuecomment-137346988  

Hi, IIUC, when the standard says implementation defined, the intent is to left the implementation some latitude to implement it as they want, however we expect that an implementation will be define some the expected behavior where.   
  
So, implementation defined has not its place in the documentation of a library the user can use.  
  
You can say that the type name returned by a function is not in the public interface, but the function must define what the user can do with its result.

---

## Comment 3

> Username: ldionne  
> Created at: 2015-09-03 09:39:52 UTC  
> Url: https://github.com/boostorg/hana/issues/122#issuecomment-137392458  

Of course, we always define what the user can do with the result. For example, the [documentation](file:///Users/ldionne/code/hana/build/doc/html/structboost_1_1hana_1_1map.html#a0f32260f859b2510dd16235b7bc011ac) for the `keys` function says "Returns a Sequence of the keys of the map, in unspecified order.". Since we know the return type is a Sequence, what the user can do with it is properly specified. That is always the case.
