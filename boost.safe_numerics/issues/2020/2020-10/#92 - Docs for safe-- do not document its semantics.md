# #92 - Docs for safe<> do not document its semantics [Closed]

> Username: akrzemi1  
> Created at: 2020-10-14 15:17:18 UTC  
> Updated at: 2020-10-17 04:07:41 UTC  
> Closed at: 2020-10-17 04:07:40 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/92  

The docs for class template `safe<T>` at https://www.boost.org/doc/libs/1_74_0/libs/safe_numerics/doc/html/safe.html do not describe semantics of the operations, but instead refer to the description of concept `Integer`. Concept `Integer`, as any other concept, describes the operations that it requires, but it does not (because it cannot) describe what these operations do.  
  
There is no way for me to figure out from the docs what the operations on `safe<T>` *do*. In particular, I would like to see which operations from the interface of `safe<T, PP, EP>` call which functions from `EP`:  
* Which operations on `safe<T, PP, EP>` can call `EP::on_undefined_behavior()`?  
* Can unary minus ever call any operation of `EP`? If so, under what conditions?  
  
These questions about semantics cannot be answered by only referring to a concept. A concept can only say what operations (are required to) exist, but not what they do.

---

## Comment 1

> Username: robertramey  
> Created at: 2020-10-14 16:05:23 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/92#issuecomment-708502404  

"Concept Integer, as any other concept, describes the operations that it requires, but it does not (because it cannot) describe what these operations do."  
  
Hmmm - I'm going to disagree with the above statement.  I think the semantic information is in there.  For types implementation the concept of SafeNumeric<T> the semantics are defined in terms of T.  If T is a primitive the semantics are defined in terms of the C++ standard.  It's a built up type, the operations on T should be defined by that type to "reflect" the exceptions of those for primitive types.  So I don't think there's any conceptual barrier.  I think it's just that I didn't include the information (in the right place)  
  
For each concept there is a table of "valid operations".  Generally this table has three columns: Expression, Result Type, and Description (or Semantics).  It is this third column which describes what the operation does.  Below that there is more information on restrictions and operations.  See for example the concept SafeNumeric.  
  
Having said the above.  I see that the ancillary information regarding valid expressions in SaveNumeric doesn't include information on exception handling (or type promotion for that matter).   So I guess this information should be added there.  Ideally one would not need this ancillary information.  But it turns out to be necessary in this case as we've made one table with expressions of the form s op t where s and t are instances of types and op is a C++ operator.  I did this so as not to have to make a new table for each operator - which would be a lot of tables.  So I built it as one table using op + ancillary information to specify "special cases" which are exceptions to the general case for all operators.  I think this is the correct decision.

---

## Comment 2

> Username: robertramey  
> Created at: 2020-10-14 16:08:36 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/92#issuecomment-708504250  

I see that in SafeNumerics - Invariants there is the following statement:  
  
"The fundamental requirement of a SafeNumeric type is that it implements all C++ operations permitted on its base type in a way the prevents the return of an incorrect arithmetic result. Various implementations of this concept may handle circumstances which produce such results differently (throw exception, compile time trap, etc..). But no implementation should return an arithmetically incorrect result."  
  
So it does say what the exceptions are regarding errors.  But clearly this is not nearly enough.  It has to layout the connection to the ExceptionPolicy and Promotion Policy.

---

## Comment 3

> Username: robertramey  
> Created at: 2020-10-17 04:07:40 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/92#issuecomment-710743510  

closing this - feel free to re-open if necessary.
