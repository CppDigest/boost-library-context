# #87 - Is this a bug in irange.hpp [Open]

> Username: PatrickMGrace  
> Created at: 2019-03-15 15:03:08 UTC  
> Updated at: 2019-04-04 16:33:50 UTC  
> Url: https://github.com/boostorg/range/issues/87  

Lines 166 to 169 in irange.hpp:  
166            reference dereference() const  
167            {  
168                return m_first + (m_step * m_step_size);  
169            }  
This looks like it is returning a reference to a temporary. Undefined behaviour?

---

## Comment 1

> Username: PatrickMGrace  
> Created at: 2019-04-04 16:33:50 UTC  
> Url: https://github.com/boostorg/range/issues/87#issuecomment-479971282  

On closer inspection it seems to be OK as reference and value_type are both defined the same, i.e. Integer.  However, it would be clearer if "reference" was replaced with "value_type" as the return type of the function.
