# #94 - Move in_place and in_place_t into individual headers? [Closed]

> Username: vinniefalco  
> Created at: 2021-08-10 01:54:00 UTC  
> Updated at: 2021-08-11 16:34:01 UTC  
> Closed at: 2021-08-10 17:09:55 UTC  
> Url: https://github.com/boostorg/optional/issues/94  

I would like to use `in_place_t` and `in_place` in my own libraries but without including optional.hpp. Can we please put these in their own headers, the way that `none` and `none_t` have their own headers?

---

## Comment 1

> Username: akrzemi1  
> Created at: 2021-08-10 06:03:51 UTC  
> Url: https://github.com/boostorg/optional/issues/94#issuecomment-895752911  

Just to clarify, names `boost::in_place` and `boost::in_place_t` are already taken in Boost and denote "in-place factories"  which was a pre-C++11 approach to perfect forwarding. They are already a separate utility library: https://www.boost.org/doc/libs/1_76_0/libs/utility/in_place_factories.html .  
  
Boost.Optional makes use of them, but at the same time, in order to preserve maximum reasonable compatibility with `std::optional` it provides its own "tag" type: `boost::in_place_init_t` and a value `boost::in_place_init`.  
  
Is the request about `boost::in_place_init_t`?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-08-10 17:09:55 UTC  
> Url: https://github.com/boostorg/optional/issues/94#issuecomment-896161484  

hmm... my goal is to implement a `trivial_optional` which is like `boost::optional` but only works for types that are trivially destructible. In other words it is a trimmed-down implementation with a more limited set of features, that has a much smaller compilation footprint.  
  
I see now that there are two "init" types and constants as you pointed out. I guess I need a substitute for `std::in_place` since this type is intended to have the same flavor as `std::optional`.  But I don't think I can use `boost::in_place` since that has a heavy footprint. I suppose I would need `boost::in_place_init`.  
  
Although now that I think about it, I should probably just not support this feature in the first place as doing so would add more lines of code which I am likely not to need. Since the types used with my trivial optional are so simple, requiring a copy instead of in-place construction is really not a big cost.  
  
So maybe I don't need a type and constant.  
  
Thanks!

---

## Comment 3

> Username: akrzemi1  
> Created at: 2021-08-11 07:06:41 UTC  
> Url: https://github.com/boostorg/optional/issues/94#issuecomment-896557173  

Might I recommend having a look at https://github.com/akrzemi1/markable/tree/develop ?  
This may not be suitable for your use case, because it takes one of the values of `T` for representing a missing value, but it is smaller and can produce faster comparison/hash, because it requires no branches.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-08-11 16:34:01 UTC  
> Url: https://github.com/boostorg/optional/issues/94#issuecomment-896975605  

Yeah, this is cool! Although.. well, the point of this is to have as few lines as possible to keep compilation resource consumption low. Example:  
```  
template<class T> class trivial_optional  
{  
  public:  
    static_assert(std::is_trivial<T>::value, "T must be trivial");  
    using value_type = T;  
  
    trivial_optional() = default;  
    trivial_optional(T value) noexcept : _value(value), _has_value(true) {}  
  
    const T *operator->() const noexcept  
    {  
        assert(_has_value);  
        return &_value;  
    }  
    T *operator->() noexcept  
    {  
        assert(_has_value);  
        return &_value;  
    }  
  
    const T &operator*() const noexcept  
    {  
        assert(_has_value);  
        return _value;  
    }  
    T &operator*() noexcept  
    {  
        assert(_has_value);  
        return _value;  
    }  
  
    T &value()  
    {  
        if (!_has_value)  
            throw std::exception();  
        return _value;  
    }  
    const T &value() const  
    {  
        if (!_has_value)  
            throw std::exception();  
        return _value;  
    }  
  
    explicit operator bool() const noexcept { return _has_value; }  
    bool has_value() const noexcept { return _has_value; }  
  
  private:  
    T _value{};  
    bool _has_value{false};  
};  
```  
  
(This came from ZeroMQ)
