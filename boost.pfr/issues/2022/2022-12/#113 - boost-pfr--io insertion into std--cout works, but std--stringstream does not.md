# #113 - boost:pfr::io insertion into std::cout works, but std::stringstream does not? [Open]

> Username: janwilmans  
> Created at: 2022-12-23 21:46:43 UTC  
> Updated at: 2022-12-24 13:28:37 UTC  
> Url: https://github.com/boostorg/pfr/issues/113  

https://cppcoach.godbolt.org/z/Mocb7znYf  
  
Only only does it not allow to stream into std::ostream / std::stringstream but also makes it "not play nice with {fmt}".  
see https://github.com/fmtlib/fmt/issues/3245  
  
in the meantime, how do I work around this?

---

## Comment 1

> Username: janwilmans  
> Created at: 2022-12-24 13:27:28 UTC  
> Updated at: 2022-12-24 13:28:37 UTC  
> Url: https://github.com/boostorg/pfr/issues/113#issuecomment-1364530324  

looking at the source code, I see there are tests for this, but they are @ifdef's out, see https://github.com/boostorg/pfr/blob/develop/test/run/read_write_non_literal.cpp#L64
