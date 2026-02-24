# #77 - Safe range weird behaviour using the automatic promotion policy. [Closed]

> Username: liarokapisv  
> Created at: 2019-11-03 13:11:54 UTC  
> Updated at: 2019-12-12 16:57:26 UTC  
> Closed at: 2019-12-12 16:57:26 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/77  

I currently have this code:  
  
```  
template <uintmax_t Min, uintmax_t Max>                                                                                                                                                                           
using urange = boost::safe_numerics::safe_unsigned_range<                                                                                                                                            
                                Min,                                                                                                                                                                              
                                Max,                                                                                                                                                                              
                                boost::safe_numerics::automatic,                                                                                                                                                  
                                boost::safe_numerics::strict_trap_policy>;                                                                                                                                        
                                                                                                                                                                                                                    
template <uintmax_t N>                                                                                                                                                                                            
constexpr auto ulit = boost::safe_numerics::safe_unsigned_literal<                                                                                                                                                
                                N,                                                                                                                                                                    
                                boost::safe_numerics::automatic,                                                                                                                                      
                                boost::safe_numerics::strict_trap_policy> {};   
  
//...  
  
urange<0,4095> x = ulit<0>; // 12 bits  
urange<0, 69615> y = x * ulit<17> // resulting type is larger than 16 bits  
y / ulit<17> ; Boom, compile-time error  
```  
I am quite baffled by this behaviour. I thought the division would produce a `urange<0, 4095>` value  
but this fail completely.  
  
Changing the promotion policy to `boost::safe_numerics::native` allows this to compile and behave as expected with the final result being `urange<0, 4095>` as it should.

---

## Comment 1

> Username: robertramey  
> Created at: 2019-11-03 19:25:35 UTC  
> Updated at: 2019-11-03 19:25:56 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/77#issuecomment-549169862  

> I thought the division would produce a urange<0, 4095> value  
but this fail completely.  
  
That I would expect as well.  I'll look into it.

---

## Comment 2

> Username: robertramey  
> Created at: 2019-11-03 19:33:00 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/77#issuecomment-549170442  

minor note re usage.  Personally I would prefer the following:  
  
`template <uintmax_t N>`  
`using unit = typename boost::safe_numerics::safe_unsigned_literal< `  
  `N,   boost::safe_numerics::automatic,  boost::safe_numerics::strict_trap_policy`  
`> ; `  
  
Personally I use the rule - never use auto - because I think it suppresses type checking.

---

## Comment 3

> Username: robertramey  
> Created at: 2019-12-12 16:57:26 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/77#issuecomment-565092546  

I've checked in changes into the develop branch to address this.  I also added a new test to the test suite to find any other cases (found at at least one more).  Runs well on my machine.  Assuming it works well on the boost test matrix, I'll merge to master.  
  
FYI, this turned out to be very subtle behavior which was very difficult to isolate.  Writing and debugging constexpr stuff is very challenging.  Thanks for finding this and producing a reasonable test case.  A variant of your test case could be run on god bolt.  The test_z test is what I use for adding user cases of pathological behavior.  You'll see your example a the end - edited to my personal taste.
