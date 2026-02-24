# #310 - Drone CI Failures with Hash Replacement [Closed]

> Username: mborland  
> Created at: 2021-03-21 13:56:05 UTC  
> Updated at: 2021-03-22 15:32:08 UTC  
> Closed at: 2021-03-22 15:32:08 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/310  

GHA and CircleCI are both green now, but it looks like there are still some failures in the Drone run from the hash replacement. Investigating.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-03-21 18:56:57 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/310#issuecomment-803641467  

Line #635 of float128.hpp is going into infinite recursion.  Missing #include for overloads of hash_value maybe?

---

## Comment 2

> Username: mborland  
> Created at: 2021-03-21 18:58:39 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/310#issuecomment-803641696  

It looks like an ADL failure. I am testing this right now:  
  
```  
inline std::size_t hash_value(const float128_backend& val)  
{  
   return boost::multiprecision::detail::hash_value(static_cast<double>(val.value()));  
}  
```

---

## Comment 3

> Username: mborland  
> Created at: 2021-03-22 15:32:08 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/310#issuecomment-804155323  

Drone and CircleCI are both good now. Closing.
