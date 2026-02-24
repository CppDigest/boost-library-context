# #32 - Question about the `create` functions inside the implementation of `boost::basic_any` [Closed]

> Username: freak82  
> Created at: 2025-11-20 12:53:51 UTC  
> Updated at: 2026-01-12 17:29:28 UTC  
> Closed at: 2026-01-12 17:29:28 UTC  
> Url: https://github.com/boostorg/any/issues/32  

Hi there,  
  
There are 4 overloads of static `create` functions in the implementation of `boost::basic_any` ([link to the code](https://github.com/boostorg/any/blob/a863df824b3606c69e8211ea850b412899e64992/include/boost/any/basic_any.hpp#L176)) which look like this:  
  
```cpp  
        template <typename ValueType>                                             
        static void create(basic_any& any, const ValueType& value, std::true_type)  
        {                                                                         
            using DecayedType = typename std::decay<const ValueType>::type;          
                                                                                  
            any.man = &small_manager<DecayedType>;                                
            new (&any.content.small_value) ValueType(value);                      
        }                                                                         
                                                                                  
        template <typename ValueType>                                             
        static void create(basic_any& any, const ValueType& value, std::false_type)  
        {                                                                            
            using DecayedType = typename std::decay<const ValueType>::type;       
                                                                                  
            any.man = &large_manager<DecayedType>;                                
            any.content.large_value = new DecayedType(value);                     
        }                                                                         
                                                                                  
        template <typename ValueType>                                             
        static void create(basic_any& any, ValueType&& value, std::true_type)     
        {                                                                         
            using DecayedType = typename std::decay<const ValueType>::type;          
            any.man = &small_manager<DecayedType>;                                
            new (&any.content.small_value) DecayedType(std::forward<ValueType>(value));  
        }                                                                         
                                                                                  
        template <typename ValueType>                                             
        static void create(basic_any& any, ValueType&& value, std::false_type)    
        {                                                                         
            using DecayedType = typename std::decay<const ValueType>::type;          
            any.man = &large_manager<DecayedType>;                                
            any.content.large_value = new DecayedType(std::forward<ValueType>(value));  
        }  
```  
Isn't the overloads which take the forwarding reference enough because they should map `const&` and `&&` cases? Or I'm mistaken?  
Also why is there added `const` in the `typename std::decay<const ValueType>::type`? Is this a copy-past mistake or again I'm missing something?  
  
I tried to compile and run the `boost::any` tests with the `const&` overloads commented out and removed `const` from the `decay` template argument and they all seem to compiled and ran fine.  
  
Regards,  
Pavel.

---

## Comment 1

> Username: apolukhin  
> Created at: 2025-12-04 15:59:50 UTC  
> Url: https://github.com/boostorg/any/issues/32#issuecomment-3612956143  

@freak82 you are right, it seems to be an artifact of dropping C++03 support https://github.com/boostorg/any/commit/f104bceb329021b2018db317521637fe4a7c3aee#diff-88089ae8fe79291ecd0951c99ccff0b8925ce79ca9ffcd59bde1e67f0a1d4da7L198  
  
Please provide a PR with removal of unnecessary overloads
