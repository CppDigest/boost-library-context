# #462 utree: Fixed double conversion [Merged]

> Username: Kojoley  
> Created at: 2019-02-13 23:16:30 UTC  
> Updated at: 2019-02-14 12:48:43 UTC  
> Merged at: 2019-02-14 12:48:40 UTC  
> Closed at: 2019-02-14 12:48:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/462  

The double conversion `spirit::string -> std::string -> spirit::string` was  
happening silently in rules because of `transform_attribute` was returning  
`std::string` and implicit constructor in `spirit::string`.  
  
The mistake could be prevented if `spirit::basic_string` constructor from  
derived class was explicit, but `utree_cast` uses `is_convertible` while  
`is_constructible` is not implemented for C++03 (it is possible, but requires  
expression SFINAE support from compiler - N2634).

---
