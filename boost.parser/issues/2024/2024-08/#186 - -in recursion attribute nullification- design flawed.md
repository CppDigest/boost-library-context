# #186 - "in recursion attribute nullification" design flawed. [Closed]

> Username: cppljevans  
> Created at: 2024-08-20 12:54:35 UTC  
> Updated at: 2024-09-30 05:51:36 UTC  
> Closed at: 2024-09-30 04:57:30 UTC  
> Url: https://github.com/boostorg/parser/issues/186  

This [code](https://github.com/tzlaine/parser/blob/develop/include/boost/parser/parser.hpp#L5063)  
causes a nested call to a rule to return nope instead of attr_type, as  
acknowledge by the comment [here](https://github.com/tzlaine/parser/blob/develop/include/boost/parser/parser.hpp#L5092).  
  
The justification for this is given [here](https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/tutorial/more_about_rules.html)  
which justifies this "in recursion attribute nullification" design with:  
  
>Recursive rules work differently from other parsers in one way: when  
re-entering the rule recursively, only the attribute variable  
(_attr(ctx) in your semantic actions) is unique to that instance of  
the rule. All the other state of the uppermost instance of that rule  
is shared. This includes the value of the rule (_val(ctx)), and the  
locals and parameters to the rule. In other words, _val(ctx) returns  
a reference to the same object in every instance of a recursive  
rule. This is because each instance of the rule needs a place to put  
the attribute it generates from its parse. However, we only want a  
single return value for the uppermost rule; if each instance had a  
separate value in _val(ctx), then it would be impossible to build up  
the result of a recursive rule step by step during in the evaluation  
of the recursive instantiations.  
  
which is false.  In particular the claim:  
  
>it would be impossible to build up the result of a recursive rule  
step by step during in the evaluation of the recursive  
instantiations.  
  
is false because the value of the uppermost rule depends on the value  
of the lowermost rule.  Discarding the value of the lowermost rule  
makes no sense to me.  
  
Furthermore, I actually have a test case of arithmetic expressions  
which shows this "in recursion attribute nullification" design is  
unneeded.  I'll be happy to upload this if you show any interest in  
examining the issue.

---

## Comment 1

> Username: cppljevans  
> Created at: 2024-09-30 05:51:35 UTC  
> Url: https://github.com/boostorg/parser/issues/186#issuecomment-2382169275  

It is not completed, unless your definition of completed is you no longer wish to pursue the issue despite my sincere effort to helpfully highlight the issue.
