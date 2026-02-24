# #650 X3: Skip rule definition injection into context [Merged]

> Username: Kojoley  
> Created at: 2021-02-19 19:06:07 UTC  
> Updated at: 2021-03-03 00:40:27 UTC  
> Merged at: 2021-03-03 00:40:25 UTC  
> Closed at: 2021-03-03 00:40:25 UTC  
> Url: https://github.com/boostorg/spirit/pull/650  

When a rule placeholder constructed and immediately consumed it cannot be used recursively, that's why the rule definition injection into a parser context can be skipped.  
This optimization has a huge impact on compile times because immediate rules are commonly used to cast an attribute like `as`/`attr_cast` does in Qi.  
  
Closes #647

---

## Comment 1

> Username: cppljevans  
> Created_at: 2021-02-19 21:07:37 UTC  
> Url: https://github.com/boostorg/spirit/pull/650#issuecomment-782352587  

>   
>   
> When a rule placeholder constructed and immediately consumed it cannot be used recursively, that's why the rule definition injection into a parser context can be skipped.  
> This optimization has a huge impact on compile times because immediate rules are commonly used to cast an attribute like `as`/`attr_cast` does in Qi.  
>   
> Closes #647  
  
Doesn't the statement:  
  
  https://github.com/Kojoley/spirit/blob/x3-skip-rule-definition-injection/include/boost/spirit/home/x3/nonterminal/detail/rule.hpp#L211  
    
only *conditionally* "skip rule definition injection into context"?  
And if that condition is not satisfied, wouldn't that still lead to  
the compile-time explosion?  Or maybe, after the 1st time, recursive  
calls are, somehow, coded so that NoSelfRef is always true on  
recursion?  But I can't see where that happens.

---

## Comment 2

> Username: Kojoley  
> Created_at: 2021-02-19 21:19:02 UTC  
> Url: https://github.com/boostorg/spirit/pull/650#issuecomment-782358863  

If you use scoped rules, it is your choice, and you pay for it. The PR fixes the cost when you are not using the feature.

---

## Comment 3

> Username: cppljevans  
> Created_at: 2021-02-20 00:21:56 UTC  
> Url: https://github.com/boostorg/spirit/pull/650#issuecomment-782473524  

>   
>   
> If you use scoped rules, it is your choice, and you pay for it. The PR fixes the cost when you are not using the feature.  
  
I'm somewhat confused by the user of several terms to mean, AFAICT,  
the same thing:  
  
* rule placeholder  
* immediate rule  
* scoped rule  
    
I assume they all mean instances of rule_definition.  Is that right?  
  
Now, here:   
  
> [attr_cast_use](https://github.com/boostorg/spirit/pull/650#issue-576658804)  
    
it says:  
  
> immediate rules are commonly used to cast an attribute   
    
which indicates an immediate rule(i.e. a rule_definition instance,  
IIUC) can be used to cast an attribute.  But, as noted elsewhere, it  
can be used recursively, at the cost (which is often unknown, at  
first, to the end-user) of increased compile-times.  Can't the "cast  
an attribute" purpose be separated from the recursive purpose?  That  
seems much more desirable from the end-user perspective.  It would  
also eliminate the need for the extra NoSelfRef flag:  
  
> [rmNoSelfRef](https://github.com/Kojoley/spirit/blob/x3-skip-rule-definition-injection/include/boost/spirit/home/x3/nonterminal/detail/rule.hpp#L156)  
  
as well as the is_default_parse_rule typedef:  
  
> [rm_def_pr](https://github.com/Kojoley/spirit/blob/x3-skip-rule-definition-injection/include/boost/spirit/home/x3/nonterminal/detail/rule.hpp#L205)  
    
Of course, maybe I'm not understanding how rule_definition is used to  
"cast an attribute". Could you provide an example, in particular, one  
which also requires the modification of the context with the ID->rhs  
mapping and thereby potentially causes the compile-time explosion?  
  
Sorry if these questions seem simplistic, but any future maintainer of  
this library would have to struggle with these same questions, in all  
probability; hence, it would be nice if the answers were documented  
somewhere and easily available to these hypothetical future  
maintainers.  
  
-regards,  
Larry

---

## Comment 4

> Username: Kojoley  
> Created_at: 2021-02-20 22:07:21 UTC  
> Url: https://github.com/boostorg/spirit/pull/650#issuecomment-782756573  

Larry, I am sorry, but requesting me to do your study every time is becoming annoying, I would prefer to spend my free time as I want to. The example is in the linked (opened by your) issue, now there are two of them, and they are fixed by this PR. The answer for 'can't users not use something like it was not suppose to be used?' is in [Hyrum's Law](https://www.hyrumslaw.com/).

---
