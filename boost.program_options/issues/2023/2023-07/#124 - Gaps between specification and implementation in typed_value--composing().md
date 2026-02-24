# #124 - Gaps between specification and implementation in typed_value::composing() [Open]

> Username: mdj982  
> Created at: 2023-07-22 17:26:57 UTC  
> Updated at: 2023-07-22 17:26:57 UTC  
> Url: https://github.com/boostorg/program_options/issues/124  

## Description and Reproduction  
  
Consider [Example 63.2](https://theboostcpplibraries.com/boost.program_options#ex.program_options_02) ([Execution on Wandbox](https://wandbox.org/permlink/UCyNgHUbG5egtL6S))  
  
> test --phone 123 --phone 456<br>  
In this case, both phone numbers, 123 and 456, are parsed. The call to composing() makes it possible to use a command-line option multiple times – the values are composed.  
  
From this description, it would seem that, conversely, without the call to `composing()`, only 123 would be assigned to the `--phone` option but 456 would be ignored. (*I assume this is the specification. Is my understanding correct?*)  
- It seems that there are others who have a similar understanding:  
  - https://boost-users.boost.narkive.com/oyPRDQxi/program-options-documentation-dead-end#post2  
  - https://stackoverflow.com/questions/8175723/vector-arguments-in-boost-program-options  
  
However, even after removing `composing()`, both 123 and 456 are assigned to `--phone`. ([Execution on Wandbox](https://wandbox.org/permlink/jCoYvofxP4GsMsDE))  
  
On the other hand, in order to confirm the effect of `composing()` in the current implementation, when `store()` is called twice, we can see that the result changes depending on whether `composing()` is called or not.  
- Twice `store()` with `composing()`: [Execution on Wandbox](https://wandbox.org/permlink/3FghGNuoJwwQBWYW)  
- Twice `store()` without `composing()`: [Execution on Wandbox](https://wandbox.org/permlink/L9JAcE0OxFzXjLGg)  
  
## Implementation Analysis  
  
From what I've seen of the implementation of Boost 1.81.0, these lines are suspect.  
- In the first for loop to proceed the parsed options ([L48-L93](https://github.com/boostorg/program_options/blob/boost-1.81.0/src/variables_map.cpp#L48-L93))  
  - The condition for skipping assignment depends on `xm.m_final`. ([L67-L68](https://github.com/boostorg/program_options/blob/boost-1.81.0/src/variables_map.cpp#L67-L68))  
  - If `is_composing()` is false, `new_final` is modified. ([L85-L91](https://github.com/boostorg/program_options/blob/boost-1.81.0/src/variables_map.cpp#L85-L91))  
    - The comment here clearly states that `composing()` affects subsequent calls to `store()`. I think this statement has a gap with that in Example 63.2.  
- After the first for loop, `xm.m_final` is modified by `new_final` ([L102](https://github.com/boostorg/program_options/blob/boost-1.81.0/src/variables_map.cpp#L102))  
  
The intuitive image from the description of Example 63.2 is an implementation in which:  
- Deleting L102.  
- Modifying L91 to `xm.m_final.insert(option_name)`  
  
## Summary  
  
- There seems to be a gap between the specification (which I can intuitively read from Example 63.2) and the implementation.  
- I would like to clarify if my understanding is wrong or if either the description of Example 63.2 or implementation is incorrect.  
  
## Version  
  
- Boost 1.81.0
