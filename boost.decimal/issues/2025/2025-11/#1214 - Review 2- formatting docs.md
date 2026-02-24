# #1214 - Review 2: formatting docs [Closed]

> Username: jzmaddock  
> Created at: 2025-11-05 19:20:51 UTC  
> Updated at: 2026-01-29 15:22:52 UTC  
> Closed at: 2025-11-06 19:19:54 UTC  
> Url: https://github.com/boostorg/decimal/issues/1214  

From the review:  
  
It is not clean from the wording whether `{fmt}` is supported conditionally, or  
always: "Boost.Decimal allows you to format your output with both `<format>`  
and `<fmt/format.h>` depending on your compiler support."  This sounds like the  
support for `<fmt/format.h>` is also conditional.  I recommend saying something  
like: "Boost.Decimal allows you to format your output with `<fmt/format.h>`  
and also with the standard `<format>` if your compiler supports it."  
  
Please use user defined literals in the format examples for the constants, it  
takes time to realize that `{ 314, -2 }` is actually `+3.14` while a decimal  
notation UDL would make it immediately obvious.  
  
There isn't much sense in using `std::endl`, the buffers are flushed at the end  
of the program.  And in practice the buffers are flushed at the end of every  
line.  And in addition to that `std::flush` does not really guarantee flushing  
all the way to the final destination.  Dietmar Kuhl has a very nice  
presentation about this.  
  
I'd remove `return 0;`.  That is the default return value of `main` and those  
lines do not add any value to the example.  
  
What are we demonstrating in this example?  If we are demonstrating the  
printing then we should have the program output with the examples.

---

## Comment 1

> Username: mborland  
> Created at: 2025-11-06 08:15:15 UTC  
> Url: https://github.com/boostorg/decimal/issues/1214#issuecomment-3495770214  

Another good use for the shiny new string constructor.
