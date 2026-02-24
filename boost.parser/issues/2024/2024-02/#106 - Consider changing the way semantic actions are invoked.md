# #106 - Consider changing the way semantic actions are invoked [Closed]

> Username: tzlaine  
> Created at: 2024-02-22 08:00:48 UTC  
> Updated at: 2024-03-15 21:29:27 UTC  
> Closed at: 2024-03-15 21:29:27 UTC  
> Url: https://github.com/boostorg/parser/issues/106  

From Phil Endicott in the Boost review:  
  
> One other advantage of having the lambda return a result could  
> be improving how the semantic type (aka attribute type) of a  
> rule is set. For example, if I have the rule  
>  
>   degrees >> minutes  
>  
> where degrees and minutes return doubles, the semantic type of  
> the combined rule is something like tuple<double,double>. But  
> I'm going to add a semantic action that adds the two values as  
> shown above, returning one double. If the return value of the  
> semantic action's lambda were used as the semantic type of the  
> rule then we could write:  
>  
>   auto rule = (degrees >> minutes)  
>                 [( [](auto d, auto m) { return d + m/60.0; } )];  
>  
> and rule would return a double to its parent rule. As it is, the  
> semantic action doesn't change the type and we have to  
> explicitly and verbosely declare the rule as returning a double.  
  
I had not thought of this before, but I really like it.  I'll have to think pretty deeply about whether there is some reason this might not work, but I'm definitely going to try to implement this.  I'm thinking that the rule would be: if std::apply(f, tup) is well-formed (where f is your invocable, and tup is your tuple of values returned by _attr()), and decltype(std::apply(f, tup)) is assignable to _val(), then do the semantic action as _val(ctx) = std::apply(f, _attr(ctx)); otherwise, the semantic action gets called with the current context..  There would be a special case when _attr() is not a tuple, but you get the idea.

---

## Comment 1

> Username: Jannik2099  
> Created at: 2024-03-07 11:30:17 UTC  
> Url: https://github.com/boostorg/parser/issues/106#issuecomment-1983314229  

To clarify, would this also work with semantic actions that are not applied over the complete rule? I.e.  
  
```cpp  
struct Parsed {  
    int i;  
    in j;  
};  
auto rule = int >> (float)[ ( [](auto f){return static_cast<int>(f)} ) ];  
```  
  
Or in other words, does this make `ctx` local to the semantic action, not the whole rule? Aiui `ctx` is always tied to the whole rule in X3.

---

## Comment 2

> Username: tzlaine  
> Created at: 2024-03-08 00:32:00 UTC  
> Url: https://github.com/boostorg/parser/issues/106#issuecomment-1984839933  

@Jannik2099 Almost.  (Technically not the way you wrote it, because `rule` is not a parser used by a rule.)  Every semantic action that would operate on a context `ctx` for which `_val(ctx)` is well-formed, can replace that semantic action with some invocable `f`, if `_val(ctx) = std::apply(_attr(ctx), f)` is well-formed (an equivalent operation is done for Hana tuples that doesn't actually use `std::apply`).  Note that this implies that `_attr(ctx)` must be a tuple.   
 Also, there is an exception -- the tuple `_attr(ctx)` must have at least two elements.  So you're example does not quite work for that reason.
