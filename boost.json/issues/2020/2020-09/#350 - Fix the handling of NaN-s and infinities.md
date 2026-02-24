# #350 - Fix the handling of NaN-s and infinities [Closed]

> Username: akrzemi1  
> Created at: 2020-09-16 22:11:23 UTC  
> Updated at: 2023-06-01 10:51:35 UTC  
> Closed at: 2023-06-01 10:51:34 UTC  
> Url: https://github.com/boostorg/json/issues/350  

The following code renders an invalid JSON content:  
  
```c++  
json::value v = std::numeric_limits<double>::infinity();  
json::serialize(v);  
```  
This is because JSON does not accept special values of `double`s. The same goes for NaN.  
  
Documentation of `json::value`'s assignment is vague. I only get signature  
  
```c++  
template<  
    class T>  
value&  
operator=(  
    T&& t);  
```  
  
(https://master.json.cpp.al/json/ref/boost__json__value/operator_eq_/overload4.html) which seems to accept about every type.  
  
`json::value`'s assignment should document whether assigning these special values is allowed or not (is it a precondition). If allowed, then the serialization function should either check for this condition and report failure, or declare a precondition that no special `double` values are present in the to-be-serialized object.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-09-16 22:18:20 UTC  
> Url: https://github.com/boostorg/json/issues/350#issuecomment-693695339  

`operator=` should read  
```  
    /** Assignment  
  
        Assigns `t` to `*this`.  
  
        @par Effects  
        @code  
        *this = value( std::forward<T>(t), this->storage() );  
        @endcode  
  
        @par Constraints  
        @code  
        std::is_constructible< value, T, storage_ptr >::value &&  
            ! std::is_same< std::remove_cvref< T >, value >::value  
        @endcode  
  
        @par Complexity  
        Constant or linear in the size of  
        `*this` plus `t`.  
  
        @par Exception Safety  
        Strong guarantee.  
        Calls to `memory_resource::allocate` may throw.  
    */  
```

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-09-16 23:39:04 UTC  
> Url: https://github.com/boostorg/json/issues/350#issuecomment-693722407  

The documentation should state somewhere, that assigning a NaN to a `json::value` results in undefined behavior.

---

## Comment 3

> Username: akrzemi1  
> Created at: 2020-09-17 15:32:47 UTC  
> Url: https://github.com/boostorg/json/issues/350#issuecomment-694315497  

Upon deeper reflection, you cannot make this a precondition for `json::value`'s assignment and construction, because you are providing a mutable access to the `double`:  
  
```c++  
void illustration(double x)  
  // precondition: x is not infinity or NaN  
{  
  json::value val = x; // ok  
  val.as_double() *= 2.0; // this can produce infinity  
}  
```  
  
So even if you make preconditions, you cannot guarantee as an invariant that `json::value` does not contain special values somewhere deep inside.  
  
The only option I see (at least for now) is to accept that `json::value` may store JSON-invalid values of type `double` and only make a precondition on the serializer that upon serializing `json::value` cannot contain JSON-invalid values. Maybe add a function in `json::value` to inspect whether it stores invalid values or not.

---

## Comment 4

> Username: pdimov  
> Created at: 2020-09-17 16:20:14 UTC  
> Url: https://github.com/boostorg/json/issues/350#issuecomment-694343079  

There's nothing undefined about assigning +inf or NaN to a `value`.

---

## Comment 5

> Username: akrzemi1  
> Created at: 2020-09-17 16:23:17 UTC  
> Url: https://github.com/boostorg/json/issues/350#issuecomment-694344824  

Agreed. So maybe the input to `serialize()` has to be guarded instead.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2020-09-17 16:25:21 UTC  
> Url: https://github.com/boostorg/json/issues/350#issuecomment-694346060  

We would have to throw an exception when serializing a double that contains a nan or infinity

---

## Comment 7

> Username: pdimov  
> Created at: 2020-09-17 17:03:11 UTC  
> Url: https://github.com/boostorg/json/issues/350#issuecomment-694368854  

Or just serialize it. If the user doesn't want inf/nan serialized, he shouldn't have put one in the `value`. /shrug

---

## Comment 8

> Username: vinniefalco  
> Created at: 2020-09-17 17:06:18 UTC  
> Url: https://github.com/boostorg/json/issues/350#issuecomment-694370659  

The serializer offers the invariant that all JSON produced will be standards-compliant. A NaN can come up from computation and it would be unreasonable to expect her to check for it after every calculation.

---

## Comment 9

> Username: pdimov  
> Created at: 2020-09-17 17:08:57 UTC  
> Url: https://github.com/boostorg/json/issues/350#issuecomment-694372136  

The reason NaN was invented was the realization that after waiting seven hours for a computation, having an exception thrown at you instead of producing output is not exactly a feature users need.

---

## Comment 10

> Username: akrzemi1  
> Created at: 2020-09-17 17:10:30 UTC  
> Url: https://github.com/boostorg/json/issues/350#issuecomment-694373019  

You can put a precondition on the serialization function: no infinities. No change in code required only in docs.

---

## Comment 11

> Username: grisumbras  
> Created at: 2023-06-01 10:51:34 UTC  
> Url: https://github.com/boostorg/json/issues/350#issuecomment-1571816190  

#884 fixed this.
