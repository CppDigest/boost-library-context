# #459 - redundant rule attribute transform [Closed]

> Username: cppljevans  
> Created at: 2019-02-09 23:13:30 UTC  
> Updated at: 2019-03-11 14:17:26 UTC  
> Closed at: 2019-03-07 11:43:11 UTC  
> Url: https://github.com/boostorg/spirit/issues/459  

This  [commit](https://github.com/boostorg/spirit/commit/147f4296735ee90efe70acd0dd53ef55ab0c906d) made on:  
  
  Sat Feb 9 19:36:59 2019 +0300  
    
still leaves redundant attribute transforms as explained  
[in this 454 comment](https://github.com/boostorg/spirit/issues/454#issuecomment-462046598)  
  
This can be demonstrated with the following program:  
  
```c++  
//Purpose:  
//  Show redundant rule attribute transformations.  
//Method:  
//  Put print statements where transformation happens  
//  by specializing the transform_attribute struct.  
//===============  
#include <iostream>  
#include <boost/spirit/home/x3.hpp>  
namespace x3=boost::spirit::x3;  
  
x3::rule<class trace_xform_id,int> trace_xform_rule="trace_xform_rule";  
namespace boost { namespace spirit { namespace x3  
{  
    template <>  
    struct transform_attribute<int,int>  
    {  
        typedef int type;  
  
        static type pre(int&attr)   
        {   
            std::cout<<"***transform_attribute<int,int>::pre:attr="<<attr<<".\n";  
            return attr;  
        }  
  
        static void post(int& val, type&& attr)  
        {  
            val=attr;  
            std::cout<<"***transform_attribute<int,int>::post:val="<<val<<".\n";  
        }  
    };  
}}}  
auto trace_xform_rule_def=x3::int_;  
BOOST_SPIRIT_DEFINE(trace_xform_rule)  
  
#include <string>  
int main()  
{  
  std::string input="1234";  
  using iter_t=std::string::iterator;  
  iter_t first=input.begin();  
  iter_t const last=input.end();  
  int attr{999};  
  bool result=  
    x3::parse  
    ( first  
    , last  
    , trace_xform_rule  
    , attr  
    );  
  std::cout<<"result="<<result<<":attr="<<attr<<"\n";  
  return 0;  
}    
```  
Which, when run, shows redundant transform::{pre,post} calls.  
  
The 454 comment also referenced a possible solution.

---

## Comment 1

> Username: Kojoley  
> Created at: 2019-02-09 23:43:18 UTC  
> Url: https://github.com/boostorg/spirit/issues/459#issuecomment-462089970  

Larry, it is called zero-cost abstractions in C++. https://godbolt.org/z/e9fkzT  
  
The output you got with your specialization is not redundant, it is what you have asked for.

---

## Comment 2

> Username: cppljevans  
> Created at: 2019-02-10 07:02:54 UTC  
> Url: https://github.com/boostorg/spirit/issues/459#issuecomment-462109195  

On 2/9/19 5:43 PM, Nikita Kniazev wrote:  
  
> Larry, it is called zero-cost abstractions in  
> C++. https://godbolt.org/z/e9fkzT  
  
There's no BOOST_SPIRIT_DEFINE in that code,  
which is essential for showing the problem of redundant  
transforms.  
  
>   
> The output you got with your specialization is not  
> redundant, it is what you have asked for.  
>  
  
Aha! Maybe you are misinterpreting what I  
meant by redundant transform.  I should have said  
**redundant transform calls**.  Obviously the  
specialization:  
  
```c++  
struct transform_attribute<int,int>  
```  
  
is redundant with respect to the attribute, and that was  
its **secondary** purpose.  
However, the **primary** purpose was  
to show spirit makes **rundundant calls** to the transform.  
  
When the code is run, the output is:  
  
```bash  
***transform_attribute<int,int>::pre:attr=999.  
***transform_attribute<int,int>::pre:attr=999.  
***transform_attribute<int,int>::post:val=1234.  
***transform_attribute<int,int>::post:val=1234.  
result=1:attr=1234  
```  
  
Which shows there are **2** calls to both the pre and post  
transforms.  Only **1** call is needed; hence, the 2nd call  
is **redundant**.  The output **should** be:  
  
```bash  
***transform_attribute<int,int>::pre:attr=999.  
***transform_attribute<int,int>::post:val=1234.  
result=1:attr=1234  
```  
  
Which is the output produced when the parser arg to the  
parse function is:  
  
```c++  
trace_xform_rule = trace_xform_rule_def //shows only 1 transform  
```  
  
Hope the above clears things up.  I tried hard to be as  
clear as possible.  If it's still not clear, then hopefully  
you or others might provide advice for making it clearer.

---

## Comment 3

> Username: Kojoley  
> Created at: 2019-02-12 15:12:54 UTC  
> Url: https://github.com/boostorg/spirit/issues/459#issuecomment-462797492  

You see a problem that does not exist. The abstractions is the way how high level languages works and it is an an optimizer job to remove redundant calls and other stuff. Until compiler does a bad job by being slow itself or producing a slow code there is nothing to worry about.  
  
  
`rule_test.cpp`  
```cpp  
#include <boost/spirit/home/x3.hpp>  
  
namespace parser {  
  
namespace x3 = boost::spirit::x3;  
  
x3::rule<class r_r, char> const r;  
auto const r_def = x3::char_;  
  
BOOST_SPIRIT_DEFINE(r)  
  
}  
  
int main(int argc, char* argv[])  
{  
    char const* s = (argc < 2 ? "a" : argv[1]), * e = s + std::strlen(s);  
    char c;  
    parse(s, e, parser::r, c);  
    return c == 'a' ? 0 : 1;  
}  
```  
  
Compared commits:  
  - after b5f1552ec2bf6382acc0f13fffd7af752cffd62a  
  - before 9ae421013875eede726bde7beedf493fa18fa36b  
  
Results:  
  
`gcc -O3 -S -fverbose-asm -g rule_test.cpp -I"/c/Working/Repositories/boost"`  
[rule_test.gcc.after.txt](https://github.com/boostorg/spirit/files/2856349/rule_test.gcc.after.txt)  
[rule_test.gcc.before.txt](https://github.com/boostorg/spirit/files/2856350/rule_test.gcc.before.txt)  
  
`cl /O2 /GL /DNDEBUG /EHsc /FAsc rule_test.cpp -I"C:\Working\Repositories\boost"`  
[rule_test.msvc.after.txt](https://github.com/boostorg/spirit/files/2856351/rule_test.msvc.after.txt)  
[rule_test.msvc.before.txt](https://github.com/boostorg/spirit/files/2856352/rule_test.msvc.before.txt)

---

## Comment 4

> Username: cppljevans  
> Created at: 2019-02-12 17:16:32 UTC  
> Url: https://github.com/boostorg/spirit/issues/459#issuecomment-462849146  

On 2/12/19 9:12 AM, Nikita Kniazev wrote:  
  
> You see a problem that does not exist. The abstractions is  
> the way how high level languages works and it is an an  
> optimizer job to remove redundant calls and other  
> stuff. Until compiler does a bad job by being slow itself  
> or producing a slow code there is nothing to worry about.  
  
I fear we're still having communication problems.  No matter what  
optimizations the compile may make, **repeating** a  
transformation is an error.  There's no way an end-user  
would expect that to happen.  
  
Hopefully the following code(a minor modificaton of the  
previous) will adequately illustrate my point:  
  
```cpp  
//Purpose:  
//  Test that only 1 attribute transformation occurs.  
//Method:  
//  specialize transform_attribute<int,int> to  
//  ++attribute.  If the transform done more than once  
//  this should be evident in the resulting value.  
//===============  
#include <boost/spirit/home/x3.hpp>  
namespace x3=boost::spirit::x3;  
  
x3::rule<class trace_xform_id,int> trace_xform_rule="trace_xform_rule";  
namespace boost { namespace spirit { namespace x3  
{  
    template <>  
    struct transform_attribute<int,int>  
    {  
        typedef int type;  
  
        static type pre(int&attr)   
        {   
            return attr;  
        }  
  
        static void post(int& val, type&& attr)  
        {  
            val=attr+1;//non-default transform  
        }  
    };  
}}}  
auto trace_xform_rule_def=x3::int_;  
BOOST_SPIRIT_DEFINE(trace_xform_rule)  
  
#include <iostream>  
int main()  
{  
  std::string input_str="1234";  
  using iter_t=std::string::iterator;  
  iter_t const first=input_str.begin();  
  iter_t const last=input_str.end();  
  int attr_defn;  
  bool result_defn=  
    x3::parse  
    ( first  
    , last  
    , trace_xform_rule = trace_xform_rule_def //only 1 transform  
    , attr_defn  
    );  
  int attr_rule;  
  bool result_rule=  
    x3::parse  
    ( first  
    , last  
    , trace_xform_rule //2 transforms  
    , attr_rule  
    );  
  //attr_defn should be same as attr_rule:  
  std::cout  
    <<"result_defn="<<result_defn<<":attr_defn="<<attr_defn<<"\n"  
    <<"result_rule="<<result_rule<<":attr_rule="<<attr_rule<<"\n"  
    ;  
  return 0;  
}    
  
```  
The output of running this is:  
  
```bash  
result_defn=1:attr_defn=1235  
result_rule=1:attr_rule=1236  
```  
  
showing different values for the attribute depending on  
whether trace_xform_rule was the parse arugment, or the  
rule_definition of same were the parse argument.  The  
end-user would expect the 2 values to be the same.  
  
If that's not an error, then I really don't understand   
how spirit is supposed to work.  
  
@Joel, anybody, is there still an error as I've described?

---

## Comment 5

> Username: Kojoley  
> Created at: 2019-02-12 17:55:22 UTC  
> Url: https://github.com/boostorg/spirit/issues/459#issuecomment-462865090  

Look, no one promised you that your transformation will be triggered only once for the attribute, if you have nested rules the transformation will also be called multiple times (before and after #456). That's how `transform_attribute` was designed and used since Qi. I can fix it by adding an overload that will bypass `transform_attribute`, but I do not see any reason to do so (and there are reasons not to do it). I showed you the assembly that supports my point (no actions needed), you showed only an ill-formed `transform_attribute` specialization.

---

## Comment 6

> Username: cppljevans  
> Created at: 2019-03-02 21:10:20 UTC  
> Url: https://github.com/boostorg/spirit/issues/459#issuecomment-468960665  

On 2/12/19 11:55 AM, Nikita Kniazev [wrote](https://github.com/boostorg/spirit/issues/459#issuecomment-462865090):  
  
@Nikita, in the following, your answers are reordered so that  
related answers are grouped together.  
  
# Incomprehensible answers  
  
> if you have nested rules the transformation will also be  
> called multiple times (before and after #456).  
  
Sorry, I see no connection between nested rules and  
redundant transform calls for a single occurrence of a  
rule. Please clarify.  
  
> That's how `transform_attribute` was designed and used  
> since Qi.  
  
AFAICT, `transform_attribute` was designed for 1 transform  
call for a single occurence of a rule in a grammar  
expression. It could *not* be designed for 2 transform calls  
because that makes no sense.  Let me emphisize that: 1  
transform call is the **only** number of transform calls  
that makes sense.  Justifying 2 transform calls because the  
2nd is redundant makes absolutely **no** sense because the  
2nd call **is** ***redundant***.  
  
# No reason to fix it answer  
  
> I can fix it by adding an overload that will bypass  
> `transform_attribute`,  
  
Obviously you mean bypass when it's redundant, and yes, that  
makes perfect sense.  Why?  Obviously because it **is**  
***redundant***.  
  
> but I do not see any reason to do so  
  
Because it makes perfect sense ;)  
  
OK, maybe I need to clarify.  The cost of this **"redundant  
transform_attribute design"** is not, as you rightly point  
out, the run-time cost, but the maintenance-time cost.  It's  
simply harder for the maintainer to understand the code with  
this "redundant transform_attribute design" and there's  
absolutely **no** reason to retain it.  
  
The only reason there is this "redundant transform_attribute  
design" is because `rule_definition::parse` is  
"schizophrenic": it serves to distinct use-cases:  
  
  1. a standalone parse function where the  
    `transform_attribute` calls actually do something useful.  
  2. a helper function, which, in combination with  
    `parse_rule` enables `rule::parse` to recursively call  
    itself.  In this case, the `rule_definition::parse`  
    `transform_attribute` call is useless because it has  
    already been called in `rule::parse`.  
  
A much better solution simply supplies another function for  
use case 2.; thus, clarifying the [program control flow].  
Let's call that the **"distinct use-case functions  
design"**.  This design would ease maintenance because 2  
functions with distinct use-case's is easier for the  
maintainer to understand than 1 function serving 2  
distinct use-case's but which decides which use-case to  
serve via a **control** parameter.  
  
As mentioned before, `rule_definition::parse` is the 1  
"schizophrenic" function serving 2 distinct  
use-case's mentioned above.  The **control** parameter is  
the attribute argument.  However, it's not an explicit  
control parameter but only an implicit one, making the  
function even more obscure.  It's implicit because the only  
way one can know that it's a [no-op] transformation is to  
understand that the `rule::parse` caller, when the  
`transform_attribute` is "well-formed", changes the `attr`  
argument `type` in such a way to make the call to  
`transform_attribute` in the subsequently called  
`rule_definition::parse` a [no-op].  
  
In summary, having 2 functions with distinct  
use-case's is obviously easier to understand than 1 function  
with a **control** parameter telling it what use-case to  
serve.  
  
> (and there are reasons not to do it).  
  
It's impossible to review said reasons if said reasons are  
not provided.  Please provide details of said reasons if  
a meaningful review of those said reasons is desired.  
  
> I showed you the assembly that supports my point (no  
> actions needed),  
  
Your points seems to be that if there's no run-time cost,  
then there's no cost.  However, as explained above, there's  
a distinct maintenance cost.  That cost is not just  
imagined, it actually happened to me.  I found it difficult  
to understand the code **because** `rule_definition::parse`  
was serving 2 distinct use-cases.  Much easier to understand  
is a **distinct use-case functions design** already  
implemented here:  
  
  - [rule_definition::parse_no_xform](https://github.com/cppljevans/spirit-experiments/blob/BOOST_SPIRIT_INSTANTIATE/include/boost/spirit/home/x3/nonterminal/rule.hpp#L63)  
  - [rule_definition::parse](https://github.com/cppljevans/spirit-experiments/blob/BOOST_SPIRIT_INSTANTIATE/include/boost/spirit/home/x3/nonterminal/rule.hpp#L87)    
  
with extensive comments to help future maintainers **easily**  
understand the 2 distinct use-cases.  
  
This is a well known design flaw.  It's been known as  
[control coupling] since the late 1960's!  So it should  
not be dismissed as unimportant. And [thoughts on  
coupling] describes the flaws mentioned above as well as  
others:  
  
```  
    Tight coupling translates into ripple effects when making  
    changes, as well as *code that is difficult to  
    understand*. It tends to propagate errors across modules,  
    when one module behaves incorrectly. It tends to  
    complicate debugging and fixing defects.  
```  
  
# "ill-formed" `transform_attribute` answers:  
  
The following answers refer to, I assume, the code here:  
  
https://github.com/boostorg/spirit/issues/459#issuecomment-462849146  
  
where the `value` of the attribute is transformed by adding  
1 to it instead of just transforming the `type` of the  
attribute.  
  
> you showed only an ill-formed `transform_attribute`  
> specialization.  
  
There's no "ill-formed" or "well-formed" defined anywhere in  
the [transform_attribute] documentation; hence, there's no  
way anyone can respond to this objection without guessing  
what you mean.  My guess is that by "well-formed"  
`tranform_attribute` you mean one that is *constrained* to  
disallow changes to the *value* of the `attribute`.  IOW,  
changes in the *type* are "well-formed", but changes in the  
*value* are "ill-formed". However, this "well-formed"  
constraint is a classic case of overspecification.  There's  
no benefit from such a constraint to end-user; hence,  
there's no justification for such overspecification; hence,  
there's no justification for the "ill-formed" description of  
the referenced *value changing* `transform_attribute`.  
  
@Nikita, if my guess about your defintion of "well-formed"  
is wrong, please provide your intended definition of  
"well-formed" and how it, in any way, benefits the  
end-users.  
  
Furthermore, by simply replacing the "redundant  
transform_attribute design", as defined above, with the  
"distinct use-case functions design", as defined above, the  
"ill-formed" `transform_attribute` problem simply  
disappears and simplifies the code.  How can there be any  
objection to that?  
  
> Look, no one promised you that your transformation will be  
> triggered only once for the attribute,   
  
You're right in that the user guide for  
[transform_attribute]  
does not **explicitly** promise to do the transformation only  
once, but that is the clear implication.  Any other  
interpretation would make no sense to the end-user. In  
addition, it should make no sense to the maintainers  
either.  Why?  Simply because the 2nd transformation call  
is **redundant**.  IOW, it serves no useful purpose but it  
still sits in the code  *obscuring that code's meaning*.  
  
[transform_attribute]: https://www.boost.org/doc/libs/1_69_0/libs/spirit/doc/html/spirit/advanced/customize/transform.html  
[control coupling]: https://en.m.wikipedia.org/wiki/Coupling_(computer_programming)  
[thoughts on coupling]: https://codurance.com/software-creation/2016/07/25/thoughts-on-coupling-in-software-design/  
[no-op]: https://whatis.techtarget.com/definition/no-op-no-operation  
[program control flow]: https://en.wikipedia.org/wiki/Control_flow

---

## Comment 7

> Username: Kojoley  
> Created at: 2019-03-02 21:24:44 UTC  
> Url: https://github.com/boostorg/spirit/issues/459#issuecomment-468961717  

Did you wrote all of this within 5 minutes after I closed the issue or it took you three weeks which you'd better spend on writing a PR that will fit all the requirements you described above, because #347 is it "tends to debugging and fixing defects" and is not "easily understand".

---

## Comment 8

> Username: cppljevans  
> Created at: 2019-03-02 21:51:15 UTC  
> Url: https://github.com/boostorg/spirit/issues/459#issuecomment-468963732  

On 3/2/19 3:24 PM, Nikita Kniazev wrote:  
> Did you wrote all of this within 5 minutes after I closed the issue or it took you three weeks which you'd better spend on writing a PR that will fit all the requirements you described above, because #347 is it "tends to debugging and fixing defects" and is not "easily understand".  
>   
Yes, you guessed right. I spent a *lot* of time trying **thoughtfully**   
answer your objections.  And yes, I did **start** to form a PR but then  
thought you'd object to such a PR by referencing your earlier objections.  
  
So, right now, if I make such a PR, will you accept it as OK  
**if** if I fix (as I've already done on my computer) the  
"tends to debugging and fixing defects" problem and fix the  
"not easily understand" problem?  
  
-regards,  
Larry

---

## Comment 9

> Username: Kojoley  
> Created at: 2019-03-02 22:00:00 UTC  
> Updated at: 2019-03-02 22:00:19 UTC  
> Url: https://github.com/boostorg/spirit/issues/459#issuecomment-468964383  

I cannot guarantee upfront. As I said, I do not see problems and I think it is fine as it is. Because I am biased I will forward the decision on your PR to @djowel.

---

## Comment 10

> Username: djowel  
> Created at: 2019-03-03 00:10:06 UTC  
> Url: https://github.com/boostorg/spirit/issues/459#issuecomment-468972943  

I'll probably need some serious pondering on this one to make an informed decision. Can we leave this open for now?

---

## Comment 11

> Username: Kojoley  
> Created at: 2019-03-03 00:11:45 UTC  
> Url: https://github.com/boostorg/spirit/issues/459#issuecomment-468973034  

Place a deadline for it then.

---

## Comment 12

> Username: djowel  
> Created at: 2019-03-03 00:14:00 UTC  
> Url: https://github.com/boostorg/spirit/issues/459#issuecomment-468973155  

> Place a deadline for it then.  
  
Fair enough. Early next week would be my target.

---

## Comment 13

> Username: djowel  
> Created at: 2019-03-04 23:55:56 UTC  
> Updated at: 2019-03-05 01:22:20 UTC  
> Url: https://github.com/boostorg/spirit/issues/459#issuecomment-469476231  

OK, I spent some time to understand the concerns raised here. I am thinking now that the simple use-case presented here does not reflect the actual intent of the purportedly "redundant" calls.  A simple change to the use case will illustrate why this is so and why it is the way it is.  
  
Try changing the rule to:  
  
`    x3::rule<class trace_xform_id, double> trace_xform_rule="trace_xform_rule";  
`  
  
Notice that I am declaring the rule attribute as double, while the call to `x3::parse` sends in an attribute of int (`attr`). This is by design and the reason why the rule's attribute (double) and the actual attribute (int) may actually be different. Try tracing through the code and see that the transforms are taking in different types. `rule::parse` takes in an attribute `int` while `call_rule_definition` now takes in an attribute of `double`. THAT is the real purpose of the attribute transforms and THIS cannot be short-circuited.

---

## Comment 14

> Username: cppljevans  
> Created at: 2019-03-06 10:22:48 UTC  
> Url: https://github.com/boostorg/spirit/issues/459#issuecomment-470052957  

On 3/4/19 5:55 PM, Joel de Guzman wrote:  
  
> OK, I spent some time to understand the concerns raised  
> here.   
  
Thanks for that, Joel.  
  
> I am thinking now that the simple use-case presented  
> here does not reflect the actual intent of the purportedly  
> "redundant" calls.  A simple change to the use case will  
> illustrate why this is so and why it is the way it is.  
   
> Try changing the rule to:  
>   
> `    x3::rule<class trace_xform_id, double> trace_xform_rule="trace_xform_rule"; `  
>  
  
Such changes are uploaded to the [less-simple use-case] gist.  
  
> Notice that I am declaring the rule attribute as double,  
> while the call to `x3::parse` sends in an attribute of int  
> (`attr`). This is by design and the reason why the rule's  
> attribute (double) and the actual attribute (int) may  
> actually be different.  Try tracing through the code and  
> see that the transforms are taking in different  
> types. `rule::parse` takes in an attribute `int`  
  
Yes, I understand that and the calls in `rule::parse` to  
transform_attribute::{pre,post}` there I understand.  
  
> while `call_rule_definition` now takes in an attribute of  
> `double`. THAT is the real purpose of the attribute  
> transforms and THIS cannot be short-circuited.  
  
However, that I don't understand.  The `transform_attribute`  
calls in `call_rule_definition` do nothing **when**  
`call_rule_definition` is called, indirectly, from  
`rule::parse`, that's what makes them "redundant"; hence,  
there's no harm in removing them from `call_rule_definition`  
and placing them in `rule_definition::parse`.  Futhermore, I  
don't see any evidence of failure when the code is run with  
these changes, which are in [PR479].  
  
Joel, could you please show an example of or explain how  
[PR479] fails?  
  
[less-simple use-case]: https://gist.github.com/cppljevans/e1fa1f6a0ebfda6bea8ae0ec1c823e41  
[PR479]: https://github.com/boostorg/spirit/pull/479

---

## Comment 15

> Username: cppljevans  
> Created at: 2019-03-06 19:14:32 UTC  
> Url: https://github.com/boostorg/spirit/issues/459#issuecomment-470237716  

On 3/6/19 4:22 AM, cppljevans wrote:  
> On 3/4/19 5:55 PM, Joel de Guzman wrote:  
  
{snip}  
>> while `call_rule_definition` now takes in an attribute of  
>> `double`. THAT is the real purpose of the attribute  
>> transforms and THIS cannot be short-circuited.  
>   
> However, that I don't understand.  The `transform_attribute`  
> calls in `call_rule_definition` do nothing **when**  
> `call_rule_definition` is called, indirectly, from  
> `rule::parse`, that's what makes them "redundant"; hence,  
> there's no harm in removing them from `call_rule_definition`  
> and placing them in `rule_definition::parse`.  Futhermore, I  
> don't see any evidence of failure when the code is run with  
> these changes, which are in [PR479].  
>   
> Joel, could you please show an example of or explain how  
> [PR479] fails?  
>   
> [less-simple use-case]: https://gist.github.com/cppljevans/e1fa1f6a0ebfda6bea8ae0ec1c823e41  
> [PR479]: https://github.com/boostorg/spirit/pull/479  
>   
>   
  
Joel, before you go any further in your decision, I've just thought  
of maybe a better way.  Why not just do the `transform_attribute` calls  
in **1** place, in the `rule_definition::parse` function?  This means  
removing the calls in `rule::parse` **and** `call_rule_definition`.  
It would also mean removing the `lambda` calls which Kojoley finds  
so objectionable and replacing it with just manual expansion of  
that call in `rule_defintion::parse`.  
  
What do you think?  
  
BTW, I'm creating another branch to test the idea out.  
  
-regards,  
Larry

---

## Comment 16

> Username: cppljevans  
> Created at: 2019-03-06 20:17:23 UTC  
> Url: https://github.com/boostorg/spirit/issues/459#issuecomment-470260121  

On 3/6/19 1:14 PM, cppljevans wrote:  
> On 3/6/19 4:22 AM, cppljevans wrote:  
>> On 3/4/19 5:55 PM, Joel de Guzman wrote:  
>   
> {snip}  
>>> while `call_rule_definition` now takes in an attribute of  
>>> `double`. THAT is the real purpose of the attribute  
>>> transforms and THIS cannot be short-circuited.  
>>  
>> However, that I don't understand.  The `transform_attribute`  
>> calls in `call_rule_definition` do nothing **when**  
>> `call_rule_definition` is called, indirectly, from  
>> `rule::parse`, that's what makes them "redundant"; hence,  
>> there's no harm in removing them from `call_rule_definition`  
>> and placing them in `rule_definition::parse`.  Futhermore, I  
>> don't see any evidence of failure when the code is run with  
>> these changes, which are in [PR479].  
>>  
>> Joel, could you please show an example of or explain how  
>> [PR479] fails?  
>>  
>> [less-simple use-case]: https://gist.github.com/cppljevans/e1fa1f6a0ebfda6bea8ae0ec1c823e41  
>> [PR479]: https://github.com/boostorg/spirit/pull/479  
>>  
>>  
>   
> Joel, before you go any further in your decision, I've just thought  
> of maybe a better way.  Why not just do the `transform_attribute` calls  
> in **1** place, in the `rule_definition::parse` function?  This means  
> removing the calls in `rule::parse` **and** `call_rule_definition`.  
> It would also mean removing the `lambda` calls which Kojoley finds  
> so objectionable and replacing it with just manual expansion of  
> that call in `rule_defintion::parse`.  
>   
> What do you think?  
>   
> BTW, I'm creating another branch to test the idea out.  
>   
OOPS.  That idea fails because `parse_rule` signature **requires**  
the `transform_attribute::pre` call to produce an argument of type  
`rule::attribute_type`.  
  
Sorry for noise.

---

## Comment 17

> Username: djowel  
> Created at: 2019-03-07 04:28:07 UTC  
> Url: https://github.com/boostorg/spirit/issues/459#issuecomment-470380250  

> However, that I don't understand. The `transform_attribute`  
> calls in `call_rule_definition` do nothing **when**  
> `call_rule_definition` is called, indirectly, from  
> `rule::parse`, that's what makes them "redundant"; hence,  
> there's no harm in removing them from `call_rule_definition`  
> and placing them in `rule_definition::parse`. Futhermore, I  
> don't see any evidence of failure when the code is run with  
> these changes, which are in [PR479](https://github.com/boostorg/spirit/pull/479).  
  
These transforms, by design, do nothing most of the time (no ops). They are  
customization hooks. They are there to allow you to place specialized   
code that will do something when needed. Only when needed. And as you  
know a big advantage of c++ is that we can have zero overhead abstractions.  
  
> Joel, could you please show an example of or explain how  
> [PR479](https://github.com/boostorg/spirit/pull/479) fails?  
  
It's not just about not failing. I'd put this in the category of if _ain't broke  
don't fix it_. IMO, the current code is not broken. I also disagree that the   
design of the transform CPs is a "bad design" as @Kojoley says. So far I   
am not convinced that it has to be replaced.

---

## Comment 18

> Username: cppljevans  
> Created at: 2019-03-07 11:42:55 UTC  
> Url: https://github.com/boostorg/spirit/issues/459#issuecomment-470494826  

On 3/6/19 10:28 PM, Joel de Guzman wrote:  
>> However, that I don't understand. The `transform_attribute`  
>> calls in `call_rule_definition` do nothing **when**  
>> `call_rule_definition` is called, indirectly, from  
>> `rule::parse`, that's what makes them "redundant"; hence,  
>> there's no harm in removing them from `call_rule_definition`  
>> and placing them in `rule_definition::parse`. Futhermore, I  
>> don't see any evidence of failure when the code is run with  
>> these changes, which are in [PR479](https://github.com/boostorg/spirit/pull/479).  
>   
> These transforms, by design, do nothing most of the time (no ops). They are  
> customization hooks. They are there to allow you to place specialized  
> code that will do something when needed. Only when needed. And as you  
> know a big advantage of c++ is that we can have zero overhead abstractions.  
>   
>> Joel, could you please show an example of or explain how  
>> [PR479](https://github.com/boostorg/spirit/pull/479) fails?  
>   
> It's not just about not failing. I'd put this in the category of if _ain't broke  
> don't fix it_. IMO, the current code is not broken.   
  
OK, thanks for the response, although I disagree with your conclusion.  
Since I'm unable to convince either you or @Kojoley, I'll close the   
comment and the PR.  
  
> I also disagree that the  
> design of the transform CPs is a "bad design" as @Kojoley says. So far I  
> am not convinced that it has to be replaced.  
>   
  
Well, I kinda agree with Kojoley, but since, as he says, he and I are  
on different planets, there's slim to no chance I'd convince him  
of whatever changes I might suggest.  
  
Best of luck in future with spirit.  
  
-regards,  
Larry

---

## Comment 19

> Username: Kojoley  
> Created at: 2019-03-07 11:44:28 UTC  
> Url: https://github.com/boostorg/spirit/issues/459#issuecomment-470495200  

> I also disagree that the design of the transform CPs is a "bad design" as @Kojoley says.  
  
I did not say it. You should have mistook my words about broken X3 rules design.

---

## Comment 20

> Username: djowel  
> Created at: 2019-03-08 00:06:00 UTC  
> Url: https://github.com/boostorg/spirit/issues/459#issuecomment-470751471  

> I did not say it. You should have mistook my words about broken X3 rules design.  
  
I'd be very interested to discuss that with you. How is it broken? I suggest opening up a ticket for this so we can discuss and flesh out details.

---

## Comment 21

> Username: djowel  
> Created at: 2019-03-08 00:17:58 UTC  
> Url: https://github.com/boostorg/spirit/issues/459#issuecomment-470754094  

> OK, thanks for the response, although I disagree with your conclusion. Since I'm unable to convince either you or @Kojoley, I'll close the comment and the PR.  
  
I'm sorry if I disappointed you, @cppljevans :-( I do value your input and your attention to detail, even when crafting responses! Perhaps I just do not understand why you think the transforms are broken, and why the "redundant" transforms you mention is an indication that it is broken. Didn't my use-case, changing the actual attribute to double, convince you that it is the way it is by design? If not, why?

---

## Comment 22

> Username: Kojoley  
> Created at: 2019-03-08 00:40:20 UTC  
> Url: https://github.com/boostorg/spirit/issues/459#issuecomment-470758754  

> > I did not say it. You should have mistook my words about broken X3 rules design.  
>  
> I'd be very interested to discuss that with you. How is it broken? I suggest opening up a ticket for this so we can discuss and flesh out details.  
  
They suffer from the static initialization order fiasco, you already know it, I think this defect is big enough to consider rules as broken by design. I do not have any ideas of fixing it, probably no much we can do about it. Other problems I have already fixed or like the one this issue is about I do not see a good solution yet.  
  
> why the "redundant" transforms you mention is an indication that it is broken.  
  
They are redundant, without quotes, here I am on the same boat with @cppljevans, but it is just not a problem as it is an internal detail and it has no performance penalties, while simplifies the code. @cppljevans seems to be uncomfortable that this internal detail can be observed and exploited by users with hooking `<T, T>` transform, I can lift the internal `<T, T>` transform so user one will never be called, however I am not sure it is really necessary.

---

## Comment 23

> Username: djowel  
> Created at: 2019-03-08 01:29:25 UTC  
> Url: https://github.com/boostorg/spirit/issues/459#issuecomment-470768650  

> > > I did not say it. You should have mistook my words about broken X3 rules design.  
> >   
> >   
> > I'd be very interested to discuss that with you. How is it broken? I suggest opening up a ticket for this so we can discuss and flesh out details.  
>   
> They suffer from the static initialization order fiasco, you already know it, I think this defect is big enough to consider rules as broken by design. I do not have any ideas of fixing it, probably no much we can do about it. Other problems I have already fixed or like the one this issue is about I do not see a good solution yet.  
>   
  
Now we're talking. Yes, I recall the static initialization order issue. I'll have another look at it and see if I can come up with something. Do you still have to the actual issue I can study?  
  
> > why the "redundant" transforms you mention is an indication that it is broken.  
>   
> They are redundant, without quotes, here I am on the same boat with @cppljevans, but it is just not a problem as it is an internal detail and it has no performance penalties, while simplifies the code. @cppljevans seems to be uncomfortable that this internal detail can be observed and exploited by users with hooking `<T, T>` transform, I can lift the internal `<T, T>` transform so user one will never be called, however I am not sure it is really necessary.  
  
I believe in reasonable, intelligent discussion and consensus. And all things being considered, I'll study this further. @cppljevans , I'll look into your suggestion about doing the `transform_attribute` calls  
 in **1** place. There might be something to it.

---

## Comment 24

> Username: Kojoley  
> Created at: 2019-03-08 01:48:15 UTC  
> Url: https://github.com/boostorg/spirit/issues/459#issuecomment-470772508  

> Now we're talking. Yes, I recall the static initialization order issue. I'll have another look at it and see if I can come up with something. Do you still have to the actual issue I can study?  
  
I do not have setup repo that I could link, through you can find examples with google on stack overflow, or try yourself placing cross calling rules in different TUs and play a Russian roulette with the compiler.

---

## Comment 25

> Username: cppljevans  
> Created at: 2019-03-09 19:15:43 UTC  
> Url: https://github.com/boostorg/spirit/issues/459#issuecomment-471213189  

>>  OK, thanks for the response, although I disagree with  
>>  your conclusion. Since I'm unable to convince either you  
>>  or @Kojoley, I'll close the comment and the PR.  
  
>I'm sorry if I disappointed you, @cppljevans :-( I do value  
>your input and your attention to detail, even when crafting  
>responses! Perhaps I just do not understand why you think  
>the transforms are broken, and why the "redundant"  
>transforms you mention is an indication that it is  
>broken. Didn't my use-case, changing the actual attribute to  
>double, convince you that it is the way it is by design? If  
>not, why?  
  
Joel, as mentioned previously, that use-case is coded in the  
[joel-use-case_gist](https://gist.github.com/cppljevans/e1fa1f6a0ebfda6bea8ae0ec1c823e41#file-x3-only_1_xform-double_int-cpp).  
And, as the `Result:` comments in the `joel-use-case_gist` indicate,  
it does not support your position.  In contrast, it does the  
opposite as indicated in the following 2 sections, which use  
type names from the above `joel-use-case_gist`:  
  
# Current design  
    
When parsing starts from `rule::parse`, this design shows  
**unneeded** calls to  
`transform_attribute<xform_attr,xform_attr>` functions from  
`call_rule_definition`.  The calls are **unneeded** because  
the **needed** `tranform_atribute<parse_attr,xform_attr>`  
calls have already been done in `rule::parse`.  In contrast,  
when parsing starts from `rule_definition::parse` the calls  
to `transform_attribute<parse_attr,xform_attr>` functions  
from `call_rule_definition` are **needed**.  
  
Now one might argue (as someone else has already done) that  
these **uneeded** calls are harmless because they have no  
run-time costs.  However, they do have a definite  
maintenance-time cost, which I myself, painfully  
experienced.  It makes the code harder to understand and the  
hazards of such [control  
coupling](https://en.m.wikipedia.org/wiki/Coupling_(computer_programming))  
design mistakes, as mentioned previously, have been  
recognized, literally, for decades.  
  
# Proposed design  
  
No matter whether parsing starts from `rule::parse` or  
`rule_definition::parse`, only the **needed**  
`transform_attribute<parse_attr,xform_attr>` calls are made.  
  
What about maintenance-cost?  One objection raised was that  
the `parser_f` lambda functions made the code harder to  
understand and debug.  That problem can easily be eliminated  
by simply manually expanding those functions inline; hence,  
that objection can easily be eliminated.

---

## Comment 26

> Username: djowel  
> Created at: 2019-03-09 23:30:17 UTC  
> Url: https://github.com/boostorg/spirit/issues/459#issuecomment-471231750  

I think I can agree that removing one transform can be an improvement, but a solution that adds more complexity is not acceptable for the problem that it is trying to solve.

---

## Comment 27

> Username: djowel  
> Created at: 2019-03-09 23:37:58 UTC  
> Url: https://github.com/boostorg/spirit/issues/459#issuecomment-471232171  

> > Now we're talking. Yes, I recall the static initialization order issue. I'll have another look at it and see if I can come up with something. Do you still have to the actual issue I can study?  
>   
> I do not have setup repo that I could link, through you can find examples with google on stack overflow, or try yourself placing cross calling rules in different TUs and play a Russian roulette with the compiler.  
  
I vaguely recall it was with one of the calc examples and the BOOST_SPIRIT_INSTANTIATE and friends and the solution was returning by value. If my memory serves me right, then it is not really the design of the rules we're talking about, instead the incorrect usage of the rules in the example.   
  
Correct me if I'm wrong.

---

## Comment 28

> Username: Kojoley  
> Created at: 2019-03-10 20:48:54 UTC  
> Updated at: 2019-03-10 20:51:41 UTC  
> Url: https://github.com/boostorg/spirit/issues/459#issuecomment-471342991  

> incorrect usage of the rules  
  
I am not an expert and I have no excellence in standard deciphering, but I read [\[basic.start.dynamic\]/4](http://eel.is/c++draft/basic.start.dynamic#4) like there is no portable usage of rules in other TU than it was defined in.  
  
https://github.com/Kojoley/spirit_init_order_fiasco  
https://travis-ci.com/Kojoley/spirit_init_order_fiasco/jobs/183681222

---

## Comment 29

> Username: djowel  
> Created at: 2019-03-10 22:43:10 UTC  
> Updated at: 2019-03-10 22:49:08 UTC  
> Url: https://github.com/boostorg/spirit/issues/459#issuecomment-471361724  

> I am not an expert and I have no excellence in standard deciphering, but I read [[basic.start.dynamic]/4](http://eel.is/c++draft/basic.start.dynamic#4) like there is no portable usage of rules in other TU than it was defined in.  
>   
> https://github.com/Kojoley/spirit_init_order_fiasco  
> https://travis-ci.com/Kojoley/spirit_init_order_fiasco/jobs/183681222  
  
Sure! I'm aware about this c++ issue and its about making the rule extern that is the problem, and not really inherent in the design of the rules in the standpoint of c++. There are a couple of solutions here. For one, the rules are just placeholders and can be returned by value from a function just fine:  
  
```  
namespace x3 = boost::spirit::x3;  
  
x3::rule<class r_b> const make_b();  
```  
  
The bug in the examples was that it was originally returned by reference, IIRC. Another solution is to make it a local static in the returning function. Something like:  
  
```  
x3::rule<class r_b> const& make_b()  
{  
    static x3::rule<class r_b> b = "b";  
    return b;  
}  
```  
  
I prefer the former now, bearing in mind that the rules are just placeholders and can be passed around by value with no problem at all. The only member variable it holds is its name.

---

## Comment 30

> Username: cppljevans  
> Created at: 2019-03-11 14:17:26 UTC  
> Url: https://github.com/boostorg/spirit/issues/459#issuecomment-471556620  

On 3/9/19 5:30 PM, Joel de Guzman wrote:  
  
> I think I can agree that removing one transform can be an improvement,   
> but a solution that adds more complexity  
  
The "adds more complexity" I don't understand.  
Could you please explain how it adds more complexity?  
I thought I'd addressed the complexity issue with the  
minor changes of simply manually expanding the lambda calls.  
  
> is not acceptable for the problem that it is trying to solve.  
>
