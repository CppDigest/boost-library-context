# #104 - _locals(), _params() - what are they for? [Closed]

> Username: akrzemi1  
> Created at: 2024-02-21 22:45:19 UTC  
> Updated at: 2024-03-15 21:29:32 UTC  
> Closed at: 2024-03-15 21:29:32 UTC  
> Url: https://github.com/boostorg/parser/issues/104  

https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/tutorial/the_parse_context.html  
  
After reading it, I have no idea what `_locals` and `_params` are for. Maybe the page should give a purpose for these two, and a motivating example. Also, no clue why two are needed.  
  
Also, in that page, the links to  `_locals()` and `_params()` are dangling.

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-02-22 08:23:54 UTC  
> Url: https://github.com/boostorg/parser/issues/104#issuecomment-1958932204  

Yeah, an example is definitely needed here.  Thanks for the dangling link notice.

---

## Comment 2

> Username: tzlaine  
> Created at: 2024-02-24 04:06:01 UTC  
> Url: https://github.com/boostorg/parser/issues/104#issuecomment-1962244007  

Dangling links fixed.  I just assumed that there were no examples when I first saw this issue, but after looking, they each have examples and explanations.  Are you looking for "when would I use this"-type of info?  At least for the `_params()`, I feel like the YAML example gives that.  Sometimes you just want to write a parameterized parser.

---

## Comment 3

> Username: akrzemi1  
> Created at: 2024-02-25 22:30:24 UTC  
> Updated at: 2024-03-03 04:34:31 UTC  
> Url: https://github.com/boostorg/parser/issues/104#issuecomment-1963083783  

The first exposure of the of the reader to `_locals()` and `_params()` is in section [The Parse Context](https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/tutorial/the_parse_context.html). In that section the reader -- I at least -- has no clue neither how these are used, nor what the motivaiton for having them is. It only has a "disclaimer" sentence: "Rules with locals are something we haven't gotten to yet". No cross-reference.  
  
I believe that the docs would benefit from a separate section "Rule State" that would separately cover rule locals, rule parameters, the way to access them via `_locals()`, `_params()` and `_p`, and better examples. Then you could link to it from [The Parse Context](https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/tutorial/the_parse_context.html).  
  
From section [More About Rules](https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/tutorial/more_about_rules.html) I still cannot figure out a couple of things:  
  
- [x] Can I only put a single type for a rule-local or can I put more? Name `_locals()` suggests that I can have more than one local in the rule, but the signature at [Struct template rule](https://tzlaine.github.io/parser/doc/html/boost/parser/rule.html) suggests that I can have only one.  
- [x] BTW, the signature at [Struct template rule](https://tzlaine.github.io/parser/doc/html/boost/parser/rule.html) fails to indicate that the third and the fourth parameters are optional: they probably have default values.  
- [x] Are types used for rule-locals need to be default-initializable?  
- [x] Are rule-locals and rule-params separate parts of rule state, and can I have both in one rule?  
- [x] Can I modify the state of rule-params?  
- [x] The YAML exaple tells me to assume a number of things, and I don't think it would compile. I still don't see a full example how I determine the parameter and then set it, and then read it.  
- [x] Given that we have `_p` is there any use case for the naked `_params()`?  
  
In a couple of places you use terms "the currently processed rule" and "the bottommost rule". I think they mean the same thing. I would recommend defining one term, putting it in the *Definitions* pge, and using it throughout the documentation.

---

## Comment 4

> Username: tzlaine  
> Created at: 2024-02-27 02:21:48 UTC  
> Url: https://github.com/boostorg/parser/issues/104#issuecomment-1965670901  

Ok, thanks for the explanations.  I agree with most of the points above.  I'll be updating this section soon.

---

## Comment 5

> Username: akrzemi1  
> Created at: 2024-03-02 08:31:27 UTC  
> Url: https://github.com/boostorg/parser/issues/104#issuecomment-1974709290  

And BTW, as someone mentioned in the review, is there still a need to have these names start with an underscore? Boost.Phoenix and Boost.Lambda needed them for obvious reasons, but bacause you endorse lambdas, the need for those magic names disappears, I guess.

---

## Comment 6

> Username: tzlaine  
> Created at: 2024-03-03 01:31:50 UTC  
> Url: https://github.com/boostorg/parser/issues/104#issuecomment-1974970238  

I still think there's a need for a naming convention here.  There's no magic, but since these functions are called without qualification, it can be hard to distinguish them from your own code.  `_foo()` immediately jumps out as "that's my code".

---

## Comment 7

> Username: akrzemi1  
> Created at: 2024-03-04 17:27:24 UTC  
> Url: https://github.com/boostorg/parser/issues/104#issuecomment-1977103519  

How about calling them with qualification: `ctx.val() = ctx.attr()`?
