# #1207 - Review 2: Construction docs [Closed]

> Username: jzmaddock  
> Created at: 2025-11-05 18:37:14 UTC  
> Updated at: 2026-01-29 15:22:52 UTC  
> Closed at: 2025-11-11 10:49:54 UTC  
> Url: https://github.com/boostorg/decimal/issues/1207  

>In the construction section I would definitely mention the user defined  
literals, because those are a much better way to represent constants values for  
humans in code than the integer+exponent.  
  
>I would reorganize the documentation parts that are about creating DFP values  
to be under a subsection Creating Decimal Floating Point Values.  Under that I  
would have the existing related topics and I would start with "Literals and  
Constants", then "Conversion from String" and only after that the other things  
because I think these will be the two most used ways of creating DFPs.  For  
constants the user defined literals are a lot more human readable than the  
integer+exponent format.  The other frequent way to get to DFP values is to get  
them from textual representation from some textual network format or from a  
file like a configuration file.  (The third likely way is values that are in  
one of the binary IEEE transfer formats.  
  
I see this as mainly about reorganising things so literals are more prominent.

---

## Comment 1

> Username: mborland  
> Created at: 2025-11-06 07:56:07 UTC  
> Url: https://github.com/boostorg/decimal/issues/1207#issuecomment-3495654566  

Since we can now construct from `std::string` and `std::string_view` when available that could probably replace early discussion of literals.
