# #439 - Support hash of hana::integral_constant with enum types [Open]

> Username: Dwarfobserver  
> Created at: 2019-02-15 14:49:45 UTC  
> Updated at: 2019-02-16 13:07:42 UTC  
> Url: https://github.com/boostorg/hana/issues/439  

Hi, first of all, thak you for this great library !  
I only use it since today, so I'm maybe missing something :  
  
`hana::integral_constant<my_enm, my_enum::xyz>` can be useful e.g. to map enum values to types, but `hana::detail::hash_integral_helper` lacks a specialization of enum types, even though `hana::integral_constant` is recognized as an `IntegralConstant` (which leads to a less explicit error than "this type is not Hashable").  
  
The fix is simple, I have a working specialization for enum types :  
  
```  
template <typename T>  
struct hash_integral_helper<T,  
    typename std::enable_if<std::is_enum<T>::value>::type  
> {  
    template <typename X>  
    static constexpr auto apply(X const&) {  
        using Integral = typename std::conditional<std::is_signed<std::underlying_type_t<T>>::value,  
            signed long long, unsigned long long  
        >::type;  
        constexpr auto x = static_cast<Integral>(X::value);  
        return hana::type_c<hana::integral_constant<Integral, x>>;  
    }  
};  
```

---

## Comment 1

> Username: ricejasonf  
> Created at: 2019-02-15 17:55:35 UTC  
> Updated at: 2019-02-15 17:56:31 UTC  
> Url: https://github.com/boostorg/hana/issues/439#issuecomment-464140899  

I'm not sure if it would be accepted, but you should submit a PR.  
  
Resulting in the same hash might be okay, but it seems wrong that two different enum types would end up being considered equal. It's just a thought, but perhaps you should make a `hana::Constant` wrapper for your enum and implement it as `hana::Hashable`.  
  
EDIT: oh wait... would it be considered equal?

---

## Comment 2

> Username: Dwarfobserver  
> Created at: 2019-02-15 20:04:28 UTC  
> Url: https://github.com/boostorg/hana/issues/439#issuecomment-464181746  

I agree, two different enums shouldn't be considered equal (even with the same underlying value).  
It seems that giving the same hash type means that the values are equal :   
https://godbolt.org/z/E6NB7Y  
  
Is this really the behavior expected or a bug ?

---

## Comment 3

> Username: ricejasonf  
> Created at: 2019-02-15 20:47:32 UTC  
> Url: https://github.com/boostorg/hana/issues/439#issuecomment-464193529  

They are actually equal under `hana::equal` so it isn't just because they have the same hash type.  
  
It boils down to `hana::equal` using `hana::is_convertible` which has special rules for converting integer types. Changing signedness is not allowed, but changing to a different sized integer type of the same size is okay.  
  
Did you try comparing integral_constants of different enum types?  
  
For enums I think you could just use `hana::type<X>` for the hash, assuming they don't compare with non-enum types or other enum types.  
  
Again, I suggest making a `hana::Constant` wrapper and bypass `hana::IntegralConstant` altogether unless you need `hana::Orderable` and all that stuff.

---

## Comment 4

> Username: Dwarfobserver  
> Created at: 2019-02-16 13:07:42 UTC  
> Url: https://github.com/boostorg/hana/issues/439#issuecomment-464345565  

Okay !   
Indeed, using `hana::type<X>` solve the problem (because otherwise, two different enums can't be compared). I don't really see the benefit of implementing a wrapper type against using `hana::integral_constant` because I then gain automatic properties for my enum, what are the downsides of `hana::integral_constant` with an enum ?  
  
I'll do a pull request in the next days :)
