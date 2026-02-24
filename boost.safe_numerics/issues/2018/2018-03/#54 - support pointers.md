# #54 - support pointers [Open]

> Username: Akrean  
> Created at: 2018-03-01 14:36:05 UTC  
> Updated at: 2018-10-16 15:11:22 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/54  

adding a safe<size_t> o to a char* p causes the compiler to generate a static_cast from an integer to a pointer, which is invalid c++

---

## Comment 1

> Username: robertramey  
> Created at: 2018-08-10 20:21:06 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/54#issuecomment-412195218  

I'm not getting this.  Please submit a little better example.

---

## Comment 2

> Username: Akrean  
> Created at: 2018-08-11 15:14:14 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/54#issuecomment-412281419  

safe<size_t> offset = 6;  
const char* ptr = "Hello World";  
ptr += offset; //<- here the compiler tells me that a static_cast from an integer to a pointer is invalid c++

---

## Comment 3

> Username: robertramey  
> Created at: 2018-08-11 15:37:23 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/54#issuecomment-412282816  

Hmmmm - wouldn't the be a good thing?  Isn't this in the spirit of the library to highlight/detect all operations which don't make arithmetic sense?  
  
I would expect your example to fail above - thus requiring the user to do the following:  
  
ptr += static_cast<size_t>(offset);  
  
which I don't see as necessarily a bad thing.  And if I were to permit that, then presumably I would have to check that the result doesn't overflow.   So implementation would look like:  
  
static_cast<unsigned long>(ptr) += offset;   
  
which would detect an overflow, but would likely not compile either due the casting the left hand side.  
  
All this raises interesting, non-trivial questions about what the library should actually do in certain cases.  I've wrestled with a bunch of similar cases and feel OK about the decisions I made.  But I have to say I'm astounded how such a simple idea ends up generating so many deep questions about what arithmetic should be in the context of fixed point numbers.  
  
TL;DR; I think about this some more.

---

## Comment 4

> Username: arvidn  
> Created at: 2018-08-13 15:54:37 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/54#issuecomment-412567690  

as a side-note: the correct type for offset is `ptrdiff_t`, not `size_t`.

---

## Comment 5

> Username: robertramey  
> Created at: 2018-08-19 17:50:10 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/54#issuecomment-414143842  

and I presume that ptrdiff_t is an alias for some sort of integer type.  
  
I've thought about it a little bit more and I've concluded that you're correct.  If I want to make a safe integer type a drop-in replacement for an integer type, I'll have to address the operation of adding to a pointer.  Unfortunately this is kind of a pain as it's one more "special" case.  I'm not going to deal with it right now as I'm focused on getting safe numerics checked into the normal boost distribution and preparing for a safe_numerics presentation at CppCon.   
  
Thanks for spotting this.  
  
Robert Ramey

---

## Comment 6

> Username: muggenhor  
> Created at: 2018-10-15 08:58:06 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/54#issuecomment-429763006  

> and I presume that ptrdiff_t is an alias for some sort of integer type.  
  
yes, from [[support.types.layout]][1]:  
  
> 2. The type `ptrdiff_t` is an implementation-defined signed integer type that can hold the difference of two  
subscripts in an array object, as described in [expr.add].  
> 3. The type `size_­t` is an implementation-defined unsigned integer type that is large enough to contain the size in bytes of any object ([expr.sizeof]).  
  
[1]: http://eel.is/c++draft/support.types#layout-2

---

## Comment 7

> Username: robertramey  
> Created at: 2018-10-16 15:11:22 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/54#issuecomment-430276419  

re-reading these comments leaves me in a quandary.  On one hand I want safe to be a drop-in replacement and on the other I want catch all "unsafe" operations.  I don't see a way to resolved these two competing goals.  Still I haven't give up hope.  My exception/error policy does permit one some flexibility (loose vs strict) to handle such situations.  I'll look in to this some more.  But it's low priority as I've got higher priority things to deal with.
