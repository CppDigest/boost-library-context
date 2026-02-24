# #9 - Would mp_switch be a useful addition? [Closed]

> Username: rhalbersma  
> Created at: 2017-08-05 22:06:29 UTC  
> Updated at: 2017-10-15 21:13:00 UTC  
> Closed at: 2017-10-15 21:13:00 UTC  
> Url: https://github.com/boostorg/mp11/issues/9  

I have sometimes use for nested `mp_if` or `std::conditional` constructs. This quickly can get cumbersome. Here's what I would like to be able to write:  
  
~~~  
template<auto N>  
using int_c = std::integral_constant<decltype(N), N>;  
  
template<auto N>  
using unsigned_ = mp_switch           
<   int_c<N>                        // will return associated type of first match  
,   mp_case<int_c< 8>, uint8_t>     // case_ has "break" semantics  
,   mp_case<int_c<16>, uint16_t>  
,   mp_case<int_c<32>, uint32_t>  
,   mp_case<int_c<64>, uint64_t>  
,   mp_default<unsigned>            // will always match, so put this last!  
>;  
  
int main()  
{  
        static_assert(std::is_same_v<unsigned_<  8>, uint8_t>);  
        static_assert(std::is_same_v<unsigned_< 16>, uint16_t>);  
        static_assert(std::is_same_v<unsigned_< 32>, uint32_t>);  
        static_assert(std::is_same_v<unsigned_< 64>, uint64_t>);  
        static_assert(std::is_same_v<unsigned_<128>, unsigned>);  
}  
~~~  
  
[Live Example](https://wandbox.org/permlink/TH3e566dSZB0P7Jl)  
If this would be a useful addition, I can send a pull request.

---

## Comment 1

> Username: pdimov  
> Created at: 2017-08-05 22:29:11 UTC  
> Url: https://github.com/boostorg/mp11/issues/9#issuecomment-320473875  

The idiomatic mp11 is something like  
  
```  
using mp_case = mp_quote<mp_eval_if_q>;  
using mp_default = mp_quote<mp_identity_t>;  
  
template<int N> using unsigned_ = mp_invoke<  
	mp_case, mp_bool<N ==  8>, uint8_t,  
	mp_case, mp_bool<N == 16>, uint16_t,  
	mp_case, mp_bool<N == 32>, uint32_t,  
	mp_case, mp_bool<N == 64>, uint64_t,  
	mp_default, unsigned  
>;  
```  
  
but it probably deserves a dedicated primitive. `mp_cond` perhaps, after Common Lisp [COND](https://www.cis.upenn.edu/~matuszek/LispText/lisp-cond.html).

---

## Comment 2

> Username: pdimov  
> Created at: 2017-10-15 21:13:00 UTC  
> Url: https://github.com/boostorg/mp11/issues/9#issuecomment-336742071  

Added `mp_cond`.
