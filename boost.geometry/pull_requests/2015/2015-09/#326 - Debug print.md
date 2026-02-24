# #326 Debug print [Closed]

> Username: jeremy-murphy  
> Created at: 2015-09-16 04:37:02 UTC  
> Updated at: 2015-09-17 06:48:32 UTC  
> Closed at: 2015-09-17 06:48:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/326  

The use of `debug_print_complement_graph()` in `polygon.hpp`, even when debugging is turned off, requires `std::cout` and therefore `<iostream>` (which is not included). This how I have plugged the hole but I understand if you want to do it differently.

---

## Comment 1

> Username: mkaravel  
> Created_at: 2015-09-16 07:48:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/326#issuecomment-140658628  

Jeremy, I am not sure I fully understand the problem. If debugging is turned off, then the empty body function `debug_print_complement_graph()` is called, but this function does not need `std::cout` to be defined.  
  
Could you please explain a bit more what the problem is?

---

## Comment 2

> Username: mkaravel  
> Created_at: 2015-09-16 07:51:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/326#issuecomment-140659034  

What I do understand though is that in regular mode, the definition of the class `complement_graph<>` needs to be defined and in fact it is not included. For this issue I would prefer to solve the problem (which is another problem) in a different way (instead of using a macro I mean).

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2015-09-16 08:14:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/326#issuecomment-140665151  

Hi Menelaos. Yeah the problem is in combination with `boost/geometry/algorithms/detail/is_valid/polygon.hpp:330`:  
  
``` C++  
debug_print_complement_graph(std::cout, g);  
```  
  
Even though the body of the debug function is empty, `std::cout` is still expected to be declared.  
  
So whenever I include `boost/geometry/algorithms/is_valid.hpp`, I either must include `iostream` before it or disable this debug function somehow, otherwise I get a compilation error on `std::cout`:  
  
```  
In file included from .../boost/geometry/algorithms/is_valid.hpp:14:  
In file included from .../boost/geometry/algorithms/detail/is_valid/implementation.hpp:15:  
.../boost/geometry/algorithms/detail/is_valid/polygon.hpp:330:47: error: no member named 'cout' in namespace 'std'; did you mean 'count'?  
```  
  
Alternatively, I could have just commented out the call to `debug_print_complement_graph`, which would make no difference to me, but I thought this would at least not break for people actually using the function.

---

## Comment 4

> Username: mkaravel  
> Created_at: 2015-09-17 06:37:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/326#issuecomment-140981978  

@jeremy-murphy The problem is crystal clear now. I have created PR #327 for fixing the issue you reported in another way. If you agree you may close this PR and I will merge into develop PR #327.

---

## Comment 5

> Username: jeremy-murphy  
> Created_at: 2015-09-17 06:48:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/326#issuecomment-140985130  

Great, thanks!

---
