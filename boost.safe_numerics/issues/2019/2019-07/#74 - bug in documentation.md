# #74 - bug in documentation [Closed]

> Username: GregKon  
> Created at: 2019-07-12 18:55:11 UTC  
> Updated at: 2019-07-12 20:18:52 UTC  
> Closed at: 2019-07-12 20:18:52 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/74  

Dear  
  
I think there is subtle bug in documentation (implementation works OK):  
https://www.boost.org/doc/libs/develop/libs/safe_numerics/doc/html/eliminate_runtime_penalty/1.html  
  
code:  
  
> int x, y;  
int z = x + y               // could overflow  
// so replace with the following:  
int x, y;  
long z = (long)x + (long)y; // can never overflow  
  
seems is incorrect, because long and int may be both 32bits and indeed, on my machine (x86_64 gcc) and ARM 32bit what I use: long and int types are 32 bits.   
See reference:  
https://en.cppreference.com/w/cpp/language/types  
int is at least 32, and long is at least 32.  
I think if you change on "short int" than it will be correct on any machine.  
  
Best Regards  
/Grzegorz

---

## Comment 1

> Username: GregKon  
> Created at: 2019-07-12 19:26:31 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/74#issuecomment-511005291  

small correction to my proposition: all types are defined  "at least .." so there is no always correct solution if used fundamental types, but my be if used fixed width types like int16_t.

---

## Comment 2

> Username: robertramey  
> Created at: 2019-07-12 20:18:52 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/74#issuecomment-511020279  

good catch.  I'm made the modification on my own machine.  eventually it will percolate to the master.
