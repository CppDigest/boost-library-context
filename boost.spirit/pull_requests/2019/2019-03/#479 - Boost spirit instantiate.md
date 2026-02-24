# #479 Boost spirit instantiate [Closed]

> Username: cppljevans  
> Created at: 2019-03-03 01:18:57 UTC  
> Updated at: 2019-03-07 11:43:49 UTC  
> Closed at: 2019-03-07 11:43:49 UTC  
> Url: https://github.com/boostorg/spirit/pull/479  

# The Problem  
  
This  
[commit](https://github.com/boostorg/spirit/commit/147f4296735ee90efe70acd0dd53ef55ab0c906d)  
allows redundant calls to the rule attribute transform  
functions.  Although the redundant calls are, in the "usual"  
cases, harmless, they obscure the meaning of the code.  This  
is because understanding if an attribute transform call *is*  
redundant in the `rule_definition::parse` function, depends  
on where it's called from.  This is a form of [control  
coupling] where a `flag` parameter is passed to a function  
(in this case, the `rule_definition::parse` function) to  
control what it does.  It's really worse than that because  
it's **implicit** control coupling.  The simple definition  
of "control coupling" is passing a flag variable to a  
function to direct that function to do something.  In this  
case, it's not a flag it's the **type** of the `attr`  
argument to `rule_definition::parse`.  Hence, there is no  
obvious **flag**, which makes it particularly obscure.  
  
How this redundancy happens is illustrated in the following  
"outline" of function calls.  In this "outline", names are  
shortened to save space and when a function definition is  
followed by {calls expanded}, the calls to functions within  
the body of that definition are "expanded" to show the call  
stack where depth of the call stack is indicated by  
indentation.  
    
```  
call_rule_definition  
{  
  transform_attribute::pre  
  parse_rhs  
  transform_attribute::post  
}  
  
parse_rule  
{  
  rule_definition::parse  
}  
  
rule_definition::parse  
{  
  call_rule_definition  
}  
  
rule::parse{calls expanded}  
{  
  transform_attribute::pre//1st pre transform.  
  parse_rule  
    rule_definition::parse  
      call_rule_definition  
        transform_attribute::pre//2nd, redundant, pre transform.  
        parse_rhs  
        transform_attribute::post//1st post transform  
  transform_attribute::post//2nd, redundant, post transform  
}    
```  
  
which shows redundant calls to the `transform_attribute::{pre,post}`'s.  
  
# The Solution  
  
The above redundancy is removed with the following changes  
to the above code:  
  
 1. encapsulate the `transform_attribute::pre; parse; transform_attribute::post`  
    actions in a *new* function, `rule_attr_transform_f(p)`,   
    where p is a parser action.  
 2. remove the transformations in `call_rule_definition`  
    since they're moved to the new `rule_attr_transform_f` function.  
 3. replace the `rule_definition::parse` call in `parse_rule` with a call to  
    a *new* function, `rule_definition::parse_no_xform` which  
    does no transforms.  
  
Here's the "outline" of the solution:  
    
```  
call_rule_definition  
{  
  parse_rhs  
}  
  
parse_rule  
{  
  rule_definition::parse_no_xform  
}  
  
rule_attr_transform_f(parse) //new function  
{  
  transform_attribute::pre  
  parse  
  transform_attribute::post  
}  
  
rule_definition::parse_no_xform //new function  
{  
  call_rule_definition  
}  
  
rule_definition::parse{calls expanded}  
{  
  rule_attr_transform_f(rule_definition::parse_no_xform)  
    transform_attribute::pre  
    rule_definition::parse_no_xform  
      call_rule_definition  
        parse_rhs  
    transform_attribute::post  
}  
  
rule::parse{calls expanded}  
{  
  rule_attr_transform_f(parse_rule)  
    transform_attribute::pre  
    parse_rule //connects rule to it's RHS  
      rule_definition::parse_no_xform  
        call_rule_definition  
          parse_rhs  
    transform_attribute::post  
}  
```  
      
The above {calls expanded} versions of:  
  
  1. `rule_definition::parse`   
  2. `rule::parse`   
    
clearly highlights the difference between the two functions:  
  
> the call to `parse_rule`.    
  
The purpose of the `parse_rule` function (although not  
documented anywhere) is to allow the `rule::parse` to call  
that rules' `rhs`.  IOW, this allows **recursive rules**.  
  
# The Benefit  
  
There's no **immediate** benefit to the proposed changes.  
It's only the **fuzzy** future benefit of more  
understandable code to future maintainers that should be  
apparent.  
  
# Previous Disscussions:  
  
See [redundant rule attribute  
transform](https://github.com/boostorg/spirit/issues/459#issuecomment-468960665)  
  
@djowel, for reasons mentioned by Nikita in the "Previous  
Discussions", could you please review this PR?  
  
[control coupling]: https://en.m.wikipedia.org/wiki/Coupling_(computer_programming)

---

## Review 1 [Commented]

> Username: Kojoley  
> Created_at: 2019-03-03 13:26:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/479#pullrequestreview-209910126  

You are fixing bad design with obfuscated code. If this improves maintainability - then I am dumb. You promised it would be better then #347, but it is the same code.

📁 test/x3/only_1_xform.cpp

```diff
  31 |+         static void post(int& val, type&& attr)
  32 |+         {
  33 |+             val=attr+1;//non-default transform
```

> Username: Kojoley  
> Created_at: 2019-03-03 13:18:19 UTC  
> Updated_at: 2019-03-03 13:26:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/479#discussion_r261866933  

It is an ill-formed transform. Just invalidate the value.

---

📁 test/x3/only_1_xform.cpp

```diff
  35 |+     };
  36 |+ }}}
  37 |+ auto xform_rule_def=x3::int_;
```

> Username: Kojoley  
> Created_at: 2019-03-03 13:18:55 UTC  
> Updated_at: 2019-03-03 13:26:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/479#discussion_r261866953  

Test nested/recursive rules too.

> Username: cppljevans  
> Created_at: 2019-03-03 17:43:04 UTC  
> Url: https://github.com/boostorg/spirit/pull/479#discussion_r261878181  

On 3/3/19 9:43 AM, Nikita Kniazev [wrote](https://github.com/boostorg/spirit/pull/479#issuecomment-469034889):  
  
> You wrote it will simplify the code, but it is not, even  
> opposite.   
  
So, let me see, you think [control coupling] is good, or  
that it's just not as bad as the solution proposed in [PR479]?  
  
> It makes debugging more painful, control flow jumps three  
> times more than currently.   
  
Again, where are these unneeded control flow jumps.  How did  
you calculate this "three times more than currently"?  I  
keep asking for specifics and you keep giving me obscure  
responses.  
  
OK, I suppose I'll have to, once again, guess your meaning :(  
  
Do you mean the use of the `parser_f` lambdas  
[here](https://github.com/boostorg/spirit/pull/479/files#diff-4af6d86666f07de96ec4a2cbd58d767aR68)  
and  
[here](https://github.com/boostorg/spirit/pull/479/files#diff-4af6d86666f07de96ec4a2cbd58d767aR145)  
is what's obfuscated?  And the unneeded control flow jumps  
are jumps into and out of these `lambdas`?  Well the obvious  
solution is to simply manually inline the code from the body  
of the `lambda`, but that's the job for the compiler, not  
the code maintainer.  Hence, I doubt that's the code your  
talking about, but who knows.  
  
OK, now it's your turn.  If the above is not what you mean  
by obfuscated code, then where is the obfuscated code, and,  
again, be specific so people don't have to keep guessing  
what you mean.  
  
> I can link Wikipedia with the meaning of obfuscation,  
> shortly "obfuscation is the deliberate act of creating  
> source or machine code that is difficult for humans to  
> understand".  
  
Of course I understand what obfuscation means.  What I don't  
understand is why you think this "wherever" code is  
obfuscated.  It's simple, show me the allegedly obfuscated  
code and maybe I can understand what you mean or how to  
clarify it.  
  
>> Aren't there are already tests for nested/recursive  
>> rules?  This PR's code has passed all tests according to  
>> what I see in [PR479].  Hence, if there are no such tests  
>> specific to nested/recursive rules, then that should be  
>> another PR.  
   
> You added a test to ensure there is no transformations on  
> the same type is called, I previously pointed you that it  
> is happening in nested/recursive rules and rule  
> definitions already, but you have chosen to ignore it.   
  
I did *not* ignore it, I didn't understand it. I also  
explicity said that  
[here](https://github.com/boostorg/spirit/issues/459#issuecomment-468960665):  
with the specific paragraph:  
  
>> Sorry, I see no connection between nested rules and  
>> redundant transform calls for a single occurrence of a  
>> rule. Please clarify.  
  
So if anyone is ignoring something, its you.  
  
> Fix this and also remove the pass-through transformation,  
  
The pass-through transformation?  What specifically are you  
talking about.  By specific, I mean file and line number, as  
I requested before but which you ignored.  
  
> this will be at least one simplifying change.  
  
Maybe, but I can't tell since you're, again, being vague.  
  
[control coupling]: https://en.m.wikipedia.org/wiki/Coupling_(computer_programming)  
[PR479]: https://github.com/boostorg/spirit/pull/479

> Username: Kojoley  
> Created_at: 2019-03-03 17:52:44 UTC  
> Url: https://github.com/boostorg/spirit/pull/479#discussion_r261878540  

Please, stop replying under unrelated comments, open GitHub, look what you are doing, this is not a fist time.


---

## Comment 2

> Username: cppljevans  
> Created_at: 2019-03-03 15:29:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/479#issuecomment-469033598  

On 3/3/19 7:26 AM, Nikita Kniazev   
[wrote](https://github.com/boostorg/spirit/pull/479#pullrequestreview-209910126):  
  
 > Kojoley commented on this pull request.  
 >  
 > You are fixing bad design with obfuscated code. If this  
 > improves maintainability - then I am dumb.  
  
Again, claims without specifics are difficult to answer  
meaningfully.  What code specifically (that means files with  
line numbers) is **obfuscated** and what makes it so?  
  
 > You promised it would be better then #347, but it is the  
 > same code.  
  
More specifically,  
[here](https://github.com/boostorg/spirit/issues/459#issuecomment-468963732),  
I promised to fix:  
  
   1. "tends to debugging and fixing defects" problem  
  
   2. "not easily understand" problem  
  
I thought 1. was referring to the problem you mentioned in  
an [earlier   
comment](https://github.com/boostorg/spirit/issues/454#issuecomment-461555968)   
  
with the sentence:  
  
 >    I am sorry, but you PR must have other problem because  
 >    of recently uncovered bugs in  
 >    make_attribute/post_transform. Try to run  
 >    ce7647d44db9ccc6e9fa031d0cb73a4436185920 tests.  
  
So, I did adapt the body of `rule_attr_transform_f` to solve  
that problem; hence, the code is *not* the same.  The  
structure is exactly the same, but the code **does** have  
differences; hence, your claim about it being the same is  
wrong.  
  
Now, about promise 2.  The solution to that was in the  
section "The Solution" in [PR479]. If that's unclear, then I  
maybe need to work on claring that up.  Could you please be  
specific about how that's unclear?  
  
Now what follows is a portion of the test code to assure  
only 1 transform takes place.  Is that the code your saying  
is obfuscated?  I don't see how.  It's pretty simple code  
and the comments at the top of the file explain clearly the  
Purpose and the Method.  
  
{snip of code from test/x3/only_1_xform.cpp}  
  
 >  
 > Test nested/recursive rules too.  
 >  
  
Aren't there are already tests for nested/recursive rules?  
This PR's code has passed all tests according to what I see  
in [PR479].  Hence, if there are no such tests specific to  
nested/recursive rules, then that should be another PR.  
  
[PR479]: (https://github.com/boostorg/spirit/pull/479).

---

## Comment 3

> Username: Kojoley  
> Created_at: 2019-03-03 15:43:57 UTC  
> Url: https://github.com/boostorg/spirit/pull/479#issuecomment-469034889  

You wrote it will simplify the code, but it is not, even opposite. It makes debugging more painful, control flow jumps three times more than currently. I can link Wikipedia with the meaning of obfuscation, shortly "obfuscation is the deliberate act of creating source or machine code that is difficult for humans to understand".  
  
> Aren't there are already tests for nested/recursive rules?  
> This PR's code has passed all tests according to what I see in [PR479].  Hence, if there are no such tests specific to nested/recursive rules, then that should be another PR.  
  
You added a test to ensure there is no transformations on the same type is called, I previously pointed you that it is happening in nested/recursive rules and rule definitions already, but you have chosen to ignore it. Fix this and also remove the pass-through transformation, this will be at least one simplifying change.

---

## Comment 4

> Username: Kojoley  
> Created_at: 2019-03-03 18:27:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/479#issuecomment-469049836  

I really do not want to waste my time arguing with you, it is pointless, because it is clear to me that we are on different planets. This is my last comment on this PR.  
  
> > You wrote it will simplify the code, but it is not, even opposite.  
>  
> So, let me see, you think control coupling is good, or that it's just not as bad as the solution proposed in PR479?  
  
I do not understand your question. For me the code after your change is hard to understand, and as consequences to debug and maintain.  
  
> How did you calculate this "three times more than currently"?  
  
Run debugger, count function calls: parse_rule vs rule_attr_transform_f + parser + parse_rule.  
  
> Do you mean the use of the parser_f lambdas here and here  
> is what's obfuscated?  
  
Yes.  
  
> And the unneeded control flow jumps are jumps into and out of these lambdas?  
  
Exactly.  
  
> Well the obvious solution is to simply manually inline the code from the body  
> of the lambda, but that's the job for the compiler, not  
> the code maintainer. Hence, I doubt that's the code your  
> talking about, but who knows.  
  
No, that's my pain too, that's the biggest problem about Qi/Karma, they are very hard to debug because of tons of jumps into different functions, methods, etc. This also bloats debug symbols size. Lambdas also bad because they do not have names and compile errors with them are unreadable.  
  
> OK, now it's your turn. If the above is not what you mean by obfuscated code, then where is the obfuscated code, and, again, be specific so people don't have to keep guessing what you mean.  
>  
> > I can link Wikipedia with the meaning of obfuscation, shortly "obfuscation is the deliberate act of creating source or machine code that is difficult for humans to understand".  
>  
> Of course I understand what obfuscation means. What I don't understand is why you think this "wherever" code is obfuscated. It's simple, show me the allegedly obfuscated code and maybe I can understand what you mean or how to clarify it.  
  
Again, it is obfuscated because it is hard to understand and debug. If you think the comments are fixing obfuscation - no, they not. The comments are first sign of obfuscated code, because if something is hard to guess from the code - it is a bad code and comments will not make it better.  
  
> > You added a test to ensure there is no transformations on the same type is called, I previously pointed you that it is happening in nested/recursive rules and rule definitions already, but you have chosen to ignore it.  
>  
> I did not ignore it, I didn't understand it. I also  
> explicity said that  
> here:  
> with the specific paragraph:  
>   
> > > Sorry, I see no connection between nested rules and redundant transform calls for a single occurrence of a rule. Please clarify.  
>   
> So if anyone is ignoring something, its you.  
>  
> > Fix this and also remove the pass-through transformation,  
>  
> The pass-through transformation? What specifically are you talking about. By specific, I mean file and line number, as I requested before but which you ignored.  
>   
> > this will be at least one simplifying change.  
>   
> Maybe, but I can't tell since you're, again, being vague.  
  
I am already started to thinking that you do not understand what you are doing.  
  
This https://github.com/boostorg/spirit/blob/5663d7d39ddba26c7b39a6bde5a0dabdf433add3/include/boost/spirit/home/x3/nonterminal/detail/transform_attribute.hpp#L33-L40  
  
And this https://github.com/boostorg/spirit/blob/5663d7d39ddba26c7b39a6bde5a0dabdf433add3/include/boost/spirit/home/x3/nonterminal/detail/transform_attribute.hpp#L47-L54

---

## Comment 5

> Username: djowel  
> Created_at: 2019-03-03 23:49:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/479#issuecomment-469078725  

> You are fixing bad design with obfuscated code. If this improves maintainability - then I am dumb.  
  
Gentlemen..., to be honest, I am lost in this forest of code and discussion. Can we take a step back, and reset and so I can reasonably step in? What are we trying to fix in the first place? The "redundant" calls? What is wrong with that? Those are customization points that should not be bypassed. It is a way to allow anyone to hook into attribute handling for customization.   
  
Also, what makes it a "bad design", @Kojoley? Let's discuss and improve, instead of throwing it all away and replacing it with another unproven design. Hartmut designed the transforms early on in Qi/Karma and inherited by X3. I disagree about it being "bad". It's proven itself over the years. Sure there can be flaws here and there, so let us have a sensible discussion and improve them.

---

## Comment 6

> Username: cppljevans  
> Created_at: 2019-03-04 11:11:34 UTC  
> Url: https://github.com/boostorg/spirit/pull/479#issuecomment-469214610  

On 3/3/19 5:49 PM, Joel de Guzman wrote:  
  
Thanks so much for stepping in.  I was despairing of  
ever making any progress.  
  
>> You are fixing bad design with obfuscated code. If this  
>> improves maintainability - then I am dumb.  
  
> Gentlemen..., to be honest, I am lost in this forest of  
> code and discussion. Can we take a step back, and reset  
> and so I can reasonably step in?   
  
I sincerely hope so!  
  
> What are we trying to fix in the first place?  
  
To avoid the code obscurity caused by the redundant calls to  
`transform_attribute` code.  
  
> The "redundant" calls? What is wrong with that?  
> Those are customization points that should not be bypassed.   
  
I fear maybe we're using a different definition of  
"redundant".  If I understand your meaning, its a  
`transform_attribute::{pre,post}` that essentially does  
nothing.  That's perfectly understandable and, as you say,  
provides a method for "customization", which is, again,  
perfectly understandable.  However, what I mean by  
"redundant" is calls to `transform_attribute::{pre,post}` in  
[call_rule_definition](https://github.com/boostorg/spirit/blob/develop/include/boost/spirit/home/x3/nonterminal/detail/rule.hpp#L296)  
in addition to the calls to the  
`transform_attribute::{pre,post}` in  
[rule::parse](https://github.com/boostorg/spirit/blob/develop/include/boost/spirit/home/x3/nonterminal/rule.hpp#L111).  
Obviously, there's no need for multiple calls to  
`transform_attribute::{pre,post}`; hence, that's why I've  
used the adjective "redundant".  
  
Does that clear things up?  
  
Again, I very much appreciate your efforts to resolve this  
issue. It's a sorely needed and difficult (I'd imagine)  
service :)

---

## Comment 7

> Username: djowel  
> Created_at: 2019-03-04 23:19:42 UTC  
> Url: https://github.com/boostorg/spirit/pull/479#issuecomment-469465172  

> Does that clear things up?  
  
I'm on it. I took some time to review the conversation and the rule code. I'll still need more time.

---

## Comment 8

> Username: djowel  
> Created_at: 2019-03-04 23:56:58 UTC  
> Url: https://github.com/boostorg/spirit/pull/479#issuecomment-469476475  

OK, I have a reply to https://github.com/boostorg/spirit/issues/459. I think there is a misunderstanding of the intent of the attribute transforms.

---
