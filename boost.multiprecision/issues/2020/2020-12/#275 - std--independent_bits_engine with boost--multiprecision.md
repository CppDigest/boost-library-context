# #275 - std::independent_bits_engine with boost::multiprecision [Closed]

> Username: Lagrang3  
> Created at: 2020-12-02 13:53:13 UTC  
> Updated at: 2020-12-03 11:27:11 UTC  
> Closed at: 2020-12-02 18:11:30 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/275  

I wonder why I cannot build an `std::independent_bits_engine` templated on a `boost::multiprecision::uint512_t`?  
  
Here's a minimal example  
```  
typedef boost::multiprecision::uint512_t integer;  
main(){  
  //...  
  //std::independent_bits_engine<std::mt19937_64,512,integer> rng1; // Error: static_assert(std::is_unsigned<_UIntType>::value  
  boost::random::independent_bits_engine<std::mt19937_64,512,integer> rng2; // ok  
  //...  
}  
```  
  
However, if I manually set the `is_unsigned` trait of `uint512_t` then `std::independent_bits_engine` compiles.   
```  
typedef boost::multiprecision::uint512_t integer;  
template <>  
struct std::is_unsigned<integer> : public std::true_type {};  
  
main(){  
  //...  
  std::independent_bits_engine<std::mt19937_64,512,integer> rng1; // ok  
  boost::random::independent_bits_engine<std::mt19937_64,512,integer> rng2; // ok  
  //...  
}  
  
```  
Is this behavior made on purpose or is it a type traits feature missing here?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-12-02 18:11:30 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/275#issuecomment-737403840  

It's the way the std is defined: none of the code in <random> is required to work with anything other than native integer types.  Even if you find a way to "fix" it for your particular compiler today, there is no guarantee that it will still work tomorrow, or with some other compiler / std lib.  BTW the type_traits `is_signed`  / `is_unsigned` are required to be false for user defined types.    
  
You could conceivably submit a feature request to your compiler vendor to use `std::numeric_limits<>::is_integer && !std::numeric_limits<>::is_signed` in place of `is_unsigned<>::value` in order to support UDT's.

---

## Comment 2

> Username: Lagrang3  
> Created at: 2020-12-02 20:01:23 UTC  
> Updated at: 2020-12-02 20:03:23 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/275#issuecomment-737462748  

@jzmaddock I don't understand.  
  
Assume `is_unsigned` is false for every user defined type, then there is no way *by construction* that I can use `std::independent_bits_engine` together with `boost::multiprecision::uint512_t`. But that doesn't make any sense.  
Without that `static_assertion` the templates play just fine.   
Abiding to this rule would be like accepting that `std::map` can only work on builtin type keys, by construction.  
  
Are you saying that `std::independent_bits_engine<std::mt19937_64,512,boost::multiprecision::uint512_t>` makes no sense?  
Does `std::independent_bits_engine<std::mt19937_64,512,Uint>` work only when `Uint` is a builtin type? I don't think so. Boost's version works out of the box `boost::random::independent_bits_engine<std::mt19937_64,512,boost::multiprecision::uint512_t>`.

---

## Comment 3

> Username: ckormanyos  
> Created at: 2020-12-02 20:33:05 UTC  
> Updated at: 2020-12-02 20:33:46 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/275#issuecomment-737479151  

> Are you saying that `std::independent_bits_engine<std::mt19937_64,512,boost::multiprecision::uint512_t>` makes no sense?  
  
I'm not sure if I completely understand the depth of the question. But I think John is saying that for User-Defined-Types most (if not all) of the features of `<type_traits>` are not defined and not foreseen to be defined by the standard.  
  
This could be a blocking point that might be at the bottom of this issue?

---

## Comment 4

> Username: Lagrang3  
> Created at: 2020-12-03 07:14:48 UTC  
> Updated at: 2020-12-03 07:17:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/275#issuecomment-737714999  

@ckormanyos Thank you for the answer. I want to understand what's going on here.  
  
To put it simple, the questions are:   
  
1. will `std::independent_bits_engine<std::mt19937_64,X,boost::multiprecision::uint512_t>` work as I would expect? That is generating random `uint512_t` from `0` to `(1<<X)- 1`, if I just ignore the `static_assertion`.  
  
2. if the answer is YES, then I don't see the point in placing such `static_assertion` enforcing `is_unsigned` knowing that custom types are not meant to satisfy the condition. Am I right, or is there a fundamental reason blocking the way?  
  
3. can you point me out where is it written that   
> the `type_traits` `is_signed` / `is_unsigned` are required to be false for user defined types.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2020-12-03 10:54:50 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/275#issuecomment-737865980  

> will std::independent_bits_engine<std::mt19937_64,X,boost::multiprecision::uint512_t> work as I would expect? That is generating random uint512_t from 0 to (1<<X)- 1, if I just ignore the static_assertion.  
  
From C++20 26.6.2.1:  
  
"Throughout this subclause 26.6, the effect of instantiating a template:   
...  
that has a template type parameter named UIntType is undefined unless the corresponding template  
argument is cv-unqualified and is one of unsigned short, unsigned int, unsigned long, or unsigned  
long long."  
  
So it's undefined behaviour.  
  
> can you point me out where is it written that the type_traits is_signed / is_unsigned are required to be false for user defined types.  
  
C++20 table 49:  
  
"is_signed:  If is_arithmetic_v<T> is true, the same result as T(-1) < T(0); otherwise, false"  
  
And for `is_arithmetic`, "T is an arithmetic type (6.8.1)", note that "arithmetic type" applies only to the built in floating point and integral types.  
  
> Boost's version works out of the box   
  
Yes, because we invested significant effort to make it so.

---

## Comment 6

> Username: Lagrang3  
> Created at: 2020-12-03 11:27:00 UTC  
> Updated at: 2020-12-03 11:27:11 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/275#issuecomment-737882304  

@jzmaddock Thank you. I won't bother anymore.   
  
> Yes, because we invested significant effort to make it so.  
  
Good job!  
  
Well, I guess `std::independent_bits_engine` and `boost::independent_bits_engine` are implemented in different ways.
