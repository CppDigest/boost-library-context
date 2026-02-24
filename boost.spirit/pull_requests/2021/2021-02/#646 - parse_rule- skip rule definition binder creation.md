# #646 parse_rule: skip rule definition binder creation [Merged]

> Username: Kojoley  
> Created at: 2021-02-08 00:47:23 UTC  
> Updated at: 2021-02-09 14:22:39 UTC  
> Merged at: 2021-02-09 14:22:36 UTC  
> Closed at: 2021-02-09 14:22:36 UTC  
> Url: https://github.com/boostorg/spirit/pull/646  

Not all of our parsers are constexpr constructible, and static local variable is not free for these.  
  
Also, closes #643.

---

## Review 1 [Changes requested]

> Username: cppljevans  
> Created_at: 2021-02-08 09:14:40 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/spirit/pull/646#pullrequestreview-585290005  

In short, you're rm'ing the 'static auto const def' and instead inlining that def.parse.  However, not quite, AFAICT.  Shouldn't  BOOST_JOIN(rule_name, _def).rhs be the 1st arg to call_rule_definition  
in order to get the RHS of the rule?

---

## Review 2 [Commented]

> Username: cppljevans  
> Created_at: 2021-02-08 16:12:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/646#pullrequestreview-585666898  

Why not just eliminate the static and use the rule_definition:parse directly instead of inlining it?  
IOW, just this one line:  
  
  return BOOST_JOIN(rule_name, _def).parse(first, last, context, unused, attr);

---

## Comment 3

> Username: Kojoley  
> Created_at: 2021-02-08 19:40:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/646#issuecomment-775394287  

> Shouldn't BOOST_JOIN(rule_name, _def).rhs be the 1st arg to call_rule_definition  
  
No  
  
> Why not just eliminate the static and use the rule_definition:parse directly instead of inlining it?  
  
Because a temporary is even worse than the static local.  
  
> IOW, just this one line:  
>   
> return BOOST_JOIN(rule_name, _def).parse(first, last, context, unused, attr);  
  
This will break annotations and debug handlers.

---

## Comment 4

> Username: djowel  
> Created_at: 2021-02-09 00:55:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/646#issuecomment-775571781  

@Kojoley  'Some checks were not successful", can you please check? I'd like to merge this.

---

## Comment 5

> Username: cppljevans  
> Created_at: 2021-02-09 09:23:32 UTC  
> Url: https://github.com/boostorg/spirit/pull/646#issuecomment-775793175  

>   
>   
> > Shouldn't BOOST_JOIN(rule_name, _def).rhs be the 1st arg to call_rule_definition  
>   
> No  
>   
> > Why not just eliminate the static and use the rule_definition:parse directly instead of inlining it?  
>   
> Because a temporary is even worse than the static local.  
>   
> > IOW, just this one line:  
> > return BOOST_JOIN(rule_name, _def).parse(first, last, context, unused, attr);  
>   
> This will break annotations and debug handlers.  
  
Ah, I see my mistake.  I had assumed, based on previous views of rule_definition use, that the  
BOOST_JOIN(rule_name,_def) was an instance of rule_definition.  However, now  
looking at all the examples and test (in particular, the https://github.com/Kojoley/spirit/blob/parse_rule-optimization/test/x3/rule_separate_tu_grammar.cpp), that none of the _def names are rule_definitions.  
Sorry for the mistake, and of course that mistake also explains why I thought BOOST_JOIN(rule_name,_def)_.parse  
would be a call to rule_definition<...>::parse.  I realize now that was a rather foolish assumption :(  
  
Sorry for noise.

---

## Comment 6

> Username: Kojoley  
> Created_at: 2021-02-09 13:03:13 UTC  
> Url: https://github.com/boostorg/spirit/pull/646#issuecomment-775920032  

> @Kojoley 'Some checks were not successful", can you please check? I'd like to merge this.  
  
Boost.Math C++11 requirement broke some of our tests, I was waiting for CI green light to push fixes to develop branch, but Travis turnaround times these days are extreme.

---
