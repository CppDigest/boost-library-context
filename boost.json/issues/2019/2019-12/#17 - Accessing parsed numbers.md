# #17 - Accessing parsed numbers [Closed]

> Username: PkmX  
> Created at: 2019-12-03 07:47:49 UTC  
> Updated at: 2019-12-04 12:46:28 UTC  
> Closed at: 2019-12-04 12:46:28 UTC  
> Url: https://github.com/boostorg/json/issues/17  

Currently a JSON number may be parsed as either `kind::int64`, `kind::uint64` or `kind::double_` depending on the actual value, e.g. `1` is parsed as `kind::int64`, `18446744073709551615` is of `kind::uint64`, and `1.0` is of `kind::double_`.  
  
This makes it convoluted to parse into a specific type if a sub-range may be parsed as another type, for example, to `uint64_t` I have to write:  
  
```cpp  
uint64_t get_as_uint64(const char* s) {  
  const auto value = json::parse(s);  
  if (const auto* n = value.if_int64(); n && *n >= 0) { // [ 0 .. 2^63 - 1 ]  
    return *n;  
  } else if (const auto* n = value.if_uint64()) { // [ 2^63 .. 2^64 - 1 ]  
    return *n;  
  } else {  
    throw std::out_of_range{"not an uint64_t"};  
  }  
}  
```  
  
whereas I would expect to just write:  
  
```cpp  
json::parse(s).as_uint64(); // should work for [0 .. 2^64 - 1] and throws on anything else  
```  
  
My intuition is that there should be a `json::number` type that can hold any parsed numbers and may be converted to the user-requested type upon access as long as there is no loss of precision:  
  
```cpp  
const json::number n = json::parse("42").as_number();  
assert(n.as_int64() == 42); // only perform expensive check if the number fits into the data type without loss of precision here  
assert(n.as_uint64() == 42);  
assert(n.as_double() == 42.0);  
// value::as_x() would just be a shorthand for value.as_number().as_x();  
  
assert(json::parse("42.0").as_number().as_int64(); == 42); // ???, debatable  
```  
  
Thoughts?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-12-03 12:41:16 UTC  
> Url: https://github.com/boostorg/json/issues/17#issuecomment-561150091  

Yes I agree that we need something, I am just not sure on what it should be or what we should call it. Although I think a separate `number` class might not be great (I had it that way originally but it was clumsy). How about this  
```  
auto jv = parse( "42 ");  
std::cout << number_cast<short>(jv);  
```  
?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-12-03 19:00:12 UTC  
> Url: https://github.com/boostorg/json/issues/17#issuecomment-561308618  

Give this a whirl? https://github.com/vinniefalco/json/pull/19

---

## Comment 3

> Username: PkmX  
> Created at: 2019-12-04 06:20:09 UTC  
> Url: https://github.com/boostorg/json/issues/17#issuecomment-561495691  

LGTM functionality-wise, though I'd prefer it to be a member function and it should also mirror the accessor functions of other value kinds:  
  
```cpp  
const auto jv = json::parse("42");  
jv.is_number<T>(); // check if the number fits into T  
jv.as_number<T>(); // checked conversion to T  
jv.get_number<T>(); // unchecked conversion to T  
jv.if_number<T>(); // pointer access  
```  
  
Then we can get rid of the `*_int64`, `*_uint64` and `*_double` functions or simply make them simply to `*_number` functions.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-12-04 12:39:14 UTC  
> Updated at: 2019-12-04 12:41:39 UTC  
> Url: https://github.com/boostorg/json/issues/17#issuecomment-561626036  

>  I'd prefer it to be a member function  
  
Hmm...I see some problems with this interface. First of all in generic contexts you would have to write `jv.template is_number<T>()` which isn't pretty. But the bigger problem is that these functions have different semantics but the same names as other functions (e.g. `is_object`).  
  
For example this cannot be implemented:  
```  
jv.if_number<short>()  
```  
  
Because a `json::value` doesn't store a `short` internally, only a `std::int64_t`.  
  
`value::get_number<T>` has different semantics from `value::get_object`. The first one performs a conversion while the second one returns a reference.  
  
`numeric_cast` is more readily understood by users since it uses the same interface as `static_cast` and `duration_cast`.  
  
Finally, the implementation of `numeric_cast` uses only public members of `json::value`, making it an ideal candidate for treatment as a free function.  
  
This is why I went with `numeric_cast`.
