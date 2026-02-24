# #575 - X3: recursive rules reach template instantiation limit too quickly? [Closed]

> Username: saki7  
> Created at: 2020-03-05 06:41:25 UTC  
> Updated at: 2025-05-09 14:37:01 UTC  
> Closed at: 2025-05-09 14:36:59 UTC  
> Url: https://github.com/boostorg/spirit/issues/575  

I was writing JSON-like grammar which have many popular types, such as "Int", "Float", "String",   
 "Variable", "Array", etc.  
  
In my X3 grammar, the content of "Array" rule contains "Variable", and "Variable" contains "Array", ... thus reached the recursion limit. I got [C1202](https://docs.microsoft.com/en-us/cpp/error-messages/compiler-errors-1/fatal-error-c1202?view=vs-2019) on Visual Studio 2019.  
  
Now please take a look at this link: [c++ - Compile times with boost spirit x3 - Stack Overflow](https://stackoverflow.com/questions/37230653/compile-times-with-boost-spirit-x3/37328539#37328539)  
  
My code was quite different from the link above; but the reason for getting recursion limit seems to be the same. That is, for some reason, X3 instantiates overly complicated expression templates.  
  
The interesting point in my code was that I had surrounded the "Variable" rule in `skip(skipper)[ ... ]`. When I removed the outer `skip(skipper)[ ... ]` directive and wrapped every single sub-rules (i.e. "Int", "Float", etc.) by exactly same skipper directives, then the error disappeared. I've also tried the approach shown in SO's answer, however it didn't help.  
  
IMHO this X3's behavior regarding instantiation limit is hard to discuss in quantitative way. My questions are:  
- Theoretically thinking, X3's rules (e.g. `my_rule`)  are like placeholders; they are separated from definitions (e.g. `my_rule_def`). So it should be possible to reduce the recursive instantiation to zero even in recursive rules, isn't it?  
- Is it possible to reduce the recursive instantiation in a generalized way, like improving the implementation details in X3?  
- If my assumption is correct, which code should I look into?

---

## Comment 1

> Username: cppljevans  
> Created at: 2020-03-05 17:00:30 UTC  
> Url: https://github.com/boostorg/spirit/issues/575#issuecomment-595336842  

On 3/5/20 12:41 AM, Nana Sakisaka wrote:  
> I was writing JSON-like grammar which have many popular types, such as  
> "Int", "Float", "String",  
> "Variable", "Array", etc.  
>  
> In my X3 grammar, the content of "Array" rule contains "Variable", and  
> "Variable" contains "Array", ... thus reached the recursion limit. I got  
> C1202  
>  
<https://docs.microsoft.com/en-us/cpp/error-messages/compiler-errors-1/fatal-error-c1202?view=vs-2019>  
> on Visual Studio 2019.  
>  
> Now please take a look at this link: c++ - Compile times with boost  
> spirit x3 - Stack Overflow  
>  
<https://stackoverflow.com/questions/37230653/compile-times-with-boost-spirit-x3/37328539#37328539>  
>  
> My code was quite different from the link above; but the reason for  
> getting recursion limit seems to be the same. That is, for some reason,  
> X3 instantiates overly complicated expression templates.  
>  
> The interesting point in my code was that I had surrounded the  
> "Variable" rule in |skip(skipper)[ ... ]|. When I removed the outer  
> |skip(skipper)[ ... ]| directive and wrapped every single sub-rules  
> (i.e. "Int", "Float", etc.) by exactly same skipper directives, then the  
> error disappeared. I've also tried the approach shown in SO's answer,  
> however it didn't help.  
>  
> IMHO this X3's behavior regarding instantiation limit is hard to discuss  
> in quantitative way. My questions are:  
>  
>   * Is it possible to reduce the recursive instantiation in a  
>     generalized way, like improving the implementation details in X3?  
>   * If my assumption is correct, which code should I look into?  
>  
> —  
Hi Nana,  
  
Based just on this part of the problem description:  
  
> The interesting point in my code was that I had surrounded the  
> "Variable" rule in |skip(skipper)[ ... ]|. When I removed the outer  
> |skip(skipper)[ ... ]| directive and wrapped every single sub-rules  
> (i.e. "Int", "Float", etc.) by exactly same skipper directives, then the  
> error disappeared. I've also tried the approach shown in SO's answer,  
> however it didn't help  
  
I'm **guessing** that the problem may be related to the one  
reported here:  
  
  
https://stackoverflow.com/questions/45282293/spirit-not-able-to-use-x3skipskippersome-recursive-rule-in-its-rule-defin  
  
Now that problem was solved by:  
  
  https://github.com/boostorg/spirit/pull/237  
  
but I'm wondering if pull/237 was only a partial solution.  Do  
you know if the context keeps getting larger as recursion  
progresses and that leads recursion limit error?  
  
-regards,  
Larry

---

## Comment 2

> Username: saki7  
> Created at: 2020-03-05 21:04:53 UTC  
> Url: https://github.com/boostorg/spirit/issues/575#issuecomment-595446375  

@cppljevans Thanks for the hints, that helps. I'll investigate and report back but it will take some time...

---

## Comment 3

> Username: Kojoley  
> Created at: 2020-03-06 02:09:19 UTC  
> Url: https://github.com/boostorg/spirit/issues/575#issuecomment-595561616  

@saki7 please provide a [minimal reproducible example](https://stackoverflow.com/help/minimal-reproducible-example) when you open a bug report.  
  
> stackoverflow.com/questions/45282293/spirit-not-able-to-use-x3skipskippersome-recursive-rule-in-its-rule-defin  
  
@cppljevans I do not think #237 is a right fix, it should build a stack instead of replacing an object.

---

## Comment 4

> Username: saki7  
> Created at: 2020-03-06 05:30:22 UTC  
> Url: https://github.com/boostorg/spirit/issues/575#issuecomment-595607345  

For now I haven't managed to reduce the code, but you might be able to find more potential test cases by googling: `"boost" "x3" "instantiation depth"`  
  
For instance, I've just confirmed that this code still hits the instantiation depth error in Boost 1.72.0, GCC HEAD: https://stackoverflow.com/questions/45899090/recursive-rule-in-spirit-x3/45900983  
  
I think I should wait until #237 is resolved. I understand the meaning of the code but I can't determine which solution is better. There's not much that I can do.

---

## Comment 5

> Username: cppljevans  
> Created at: 2020-03-06 12:46:47 UTC  
> Url: https://github.com/boostorg/spirit/issues/575#issuecomment-595752725  

On 3/5/20 11:30 PM, Nana Sakisaka wrote:  
[snip]  
> For instance, I've just confirmed that this code still hits the  
> instantiation depth error in Boost 1.72.0, GCC HEAD:  
> https://stackoverflow.com/questions/45899090/recursive-rule-in-spirit-x3/45900983  
  
Nana, it's unclear from above whether you're referring to the OP code or  
the code supplied by sehe.  
  
The following code:  
  
  https://gist.github.com/cppljevans/86770e37767b38f3aa0cceaea06f4407  
  
provides an answer.  When !defined(USE_TOP_LEVEL_SKIPPER), the infinite  
template instantiation error occurs.  Since the compilation was done  
with boost 1.72.0, the current boost, which includes pull/237 (IIUC),  
does not work, as Nana says.  
  
>   
> I think I should wait until #237  
> <https://github.com/boostorg/spirit/pull/237> is resolved. I understand  
> the meaning of the code but I can't determine which solution is better.  
> There's not much that I can do.  
[snip]

---

## Comment 6

> Username: cppljevans  
> Created at: 2020-03-06 13:26:44 UTC  
> Url: https://github.com/boostorg/spirit/issues/575#issuecomment-595766780  

On 3/5/20 11:30 PM, Nana Sakisaka wrote:  
[snip]  
  
> I think I should wait until #237  
> <https://github.com/boostorg/spirit/pull/237> is resolved. I understand  
> the meaning of the code but I can't determine which solution is better.  
> There's not much that I can do.  
You could try the **possible** solution mentioned here:  
  
  https://sourceforge.net/p/spirit/mailman/message/36058840/  
  
but the code has moved to here:  
  
  
https://github.com/cppljevans/spirit_exp-get_rhs/tree/get_rhs/workbench/x3/workbench-get_rhs/HanWang_make_context  
  
and it's not up-to-date, but you could make it so for forking  
and updating, if you want to work that hard :(  
  
[snip]

---

## Comment 7

> Username: saki7  
> Created at: 2020-03-06 18:07:56 UTC  
> Url: https://github.com/boostorg/spirit/issues/575#issuecomment-595891342  

I think we need more clarification here.  
  
> @cppljevans wrote:  
>> @saki7 wrote:  
>> For instance, I've just confirmed that this code still hits the  
>> instantiation depth error in Boost 1.72.0, GCC HEAD:  
>> https://stackoverflow.com/questions/45899090/recursive-rule-in-spirit-x3/45900983  
>  
> Nana, it's unclear from above whether you're referring to the OP code or  
> the code supplied by sehe.  
  
I was referring to the OP code. Therefore,  
  
> @cppljevans wrote:  
> The following code:  
>  https://gist.github.com/cppljevans/86770e37767b38f3aa0cceaea06f4407  
> provides an answer.  When !defined(USE_TOP_LEVEL_SKIPPER), the infinite  
> template instantiation error occurs.    
  
This gist correctly illustrates the intent of my argument. (Thanks for writing this snippet)  
  
> @cppljevans wrote:  
> Since the compilation was done  
with boost 1.72.0, the current boost, which includes pull/237 (IIUC),  
does not work, as Nana says.  
  
#237 is abandoned. You could see that boost/spirit/home/x3/support/context.hpp has not been updated since 2016 on the develop branch. If my understanding is correct, the recursive instantiation depth issue still persists, and no fix was ever made.  
  
> https://sourceforge.net/p/spirit/mailman/message/36058840/  
> https://github.com/cppljevans/spirit_exp-get_rhs/tree/get_rhs/workbench/x3/workbench-get_rhs/HanWang_make_context  
  
If #237 is abandoned then the solutions shown in above links is invalidated. I don't quite get the benefit for investigating on closed PR...  
  
However @Kojoley said:  
> @cppljevans I do not think #237 is a right fix, it should build a stack instead of replacing an object.  
  
If #237 is wrong then we need an another approach, right?

---

## Comment 8

> Username: saki7  
> Created at: 2020-03-06 18:13:55 UTC  
> Url: https://github.com/boostorg/spirit/issues/575#issuecomment-595893661  

BTW, an another possibility regarding the cause of instantiation depth error is some other design failure which is not related to the context creation. Currently we are just assuming (or guessing) that the cause is related to context creation in skippers. In my experience I've seen same errors for different kind of grammars, however I can't recall the exact code. We could just focus on fixing #237 for now, though.

---

## Comment 9

> Username: cppljevans  
> Created at: 2020-03-06 18:16:52 UTC  
> Url: https://github.com/boostorg/spirit/issues/575#issuecomment-595894760  

On 3/6/20 7:26 AM, cppljevans wrote:  
[snip]  
> You could try the **possible** solution mentioned here:  
>   
> https://sourceforge.net/p/spirit/mailman/message/36058840/  
>   
> but the code has moved to here:  
>   
> https://github.com/cppljevans/spirit_exp-get_rhs/tree/get_rhs/workbench/x3/workbench-get_rhs/HanWang_make_context  
>   
> and it's not up-to-date, but you could make it so for forking  
> and updating, if you want to work that hard :(  
>   
OOPS, that proposed solution in workbench-get_rhs is not working now;  
hence, please ignore the above suggestion to try it :((

---

## Comment 10

> Username: cppljevans  
> Created at: 2020-03-06 20:29:25 UTC  
> Url: https://github.com/boostorg/spirit/issues/575#issuecomment-595952353  

On 3/6/20 12:13 PM, Nana Sakisaka wrote:  
> BTW, an another possibility regarding the cause of instantiation depth  
> error is some other design failure which is not related to the context  
> creation. Currently we are just assuming (or guessing) that the cause is  
> related to context creation in skippers. In my experience I've seen same  
> errors for different kind of grammars, however I can't recall the exact  
> code. We could just focus on fixing #237  
> <https://github.com/boostorg/spirit/pull/237> for now, though.  
>   
  
Nana, I cloned the PR with the command:  
  
   clone https://github.com/think-cell/spirit.git -b  
thinkcell_recursive_context_fix thinkcell_recursive_context_fix  
  
then put that directory at the head of the include directories  
on the command line, an it was able to compile the  
sehe-coliru-stackoverflow-answer.cpp file mentioned in one of my previous  
posts.  You might do the same clone and see if you can compile  
the code you're having problems with.  
  
-Larry

---

## Comment 11

> Username: saki7  
> Created at: 2020-03-06 21:20:22 UTC  
> Url: https://github.com/boostorg/spirit/issues/575#issuecomment-595970054  

Larry, I understand that the diff proposed in #237 is able to 'fix' the error in certain branches. But the branch is from 2017, and also @Kojoley said that the solution is inappropriate:  
  
> @cppljevans I do not think #237 is a right fix, it should build a stack instead of replacing an object.  
  
If the approach described in #237 is going to be rejected by the maintainer, then there's no point of investigating #237's branch now.  
  
---  
  
Lets discuss about the possible implementation.  
  
I understand the rationale for 'building a stack' instead of uniquely replacing an object; however the actual implementation apparently is more complicated than to just building a stack.  
  
If we build the stack on compile-time, then the meta-type for stack gets infinitely instantiated. If we do that runtime, it leads to infinite memory allocation. Anyways, we must 'cut' the recursion at some point.  
  
For instance, boost::recursive_wrapper handles the recursion appropriately, but I'm not aware of the real C++ trick in boost::variant.

---

## Comment 12

> Username: cppljevans  
> Created at: 2020-03-06 22:34:51 UTC  
> Url: https://github.com/boostorg/spirit/issues/575#issuecomment-595993641  

On 3/6/20 3:20 PM, Nana Sakisaka wrote:  
> Larry, I understand that the diff proposed in #237  
> <https://github.com/boostorg/spirit/pull/237> is able to 'fix' the error  
> in certain branches. But the branch is from 2017, and also @Kojoley  
> <https://github.com/Kojoley> said that the solution is inappropriate:  
>  
>     @cppljevans <https://github.com/cppljevans> I do not think #237  
>     <https://github.com/boostorg/spirit/pull/237> is a right fix, it  
>     should build a stack instead of replacing an object.  
>  
> If the approach described in #237  
> <https://github.com/boostorg/spirit/pull/237> is going to be rejected by  
> the maintainer, then there's no point of investigating #237  
> <https://github.com/boostorg/spirit/pull/237>'s branch now.  
>  
> ------------------------------------------------------------------------  
>  
> Lets discuss about the possible implementation.  
>  
> I understand the rationale for 'building a stack' instead of uniquely  
> replacing an object; however the actual implementation apparently is  
> more complicated than to just building a stack.  
  
I did not understand what 'building a stack'.  I guess maybe  
it means replacing the storage of the skipper in the context  
with another argument, something called skipper_stack, and  
maybe storing the skipper there **with** a tag_type flagging  
the "top" of the stack and only using the skipper  
at the "top" of that stack, **and** only push another skipper  
on the skipper_stack if such a skipper is not already on the  
stack somewhere.  Thus, even with recursion, there would  
only be a limited number of skipper's in the skipper_stack.  
However, I don't see how that's any better than the #237  
solution because, IIRC, that's essentially what #237 does.  
  
Maybe @Kojoley could clarify his design a bit.  
  
>  
> If we build the stack on compile-time, then the meta-type for stack gets  
> infinitely instantiated. If we do that runtime, it leads to infinite  
> memory allocation. Anyways, we must 'cut' the recursion at  
> some point.  
  
I think PR 237 does 'cut' the recursion in the manner I  
described above.  Of course it has been a while since I  
looked at the code; hence, I maybe missing something :(  
  
>  
> For instance, boost::recursive_wrapper handles the recursion  
> appropriately, but I'm not aware of the real C++ trick in boost::variant.  
>  
  
-regards,  
Larry

---

## Comment 13

> Username: Kojoley  
> Created at: 2020-03-06 23:54:21 UTC  
> Updated at: 2020-03-06 23:54:32 UTC  
> Url: https://github.com/boostorg/spirit/issues/575#issuecomment-596012759  

@cppljevans I was concerned that after #237 the following code will print `42 123 123` instead of `42 123 42`, but it is not a case. I am still did not dig into a problem to evaluate it. Also, that PR does not have tests (and even no reproducer was embed into the issue thread we are chatting in).  
  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <iostream>  
  
class tag {};  
  
namespace x3 = boost::spirit::x3;  
  
auto _print_tag_value = [](auto const& ctx){  
    std::cout << x3::get<tag>(ctx) << ' ';  
};  
  
auto print_tag_value = x3::eps[_print_tag_value];  
  
int main()  
{  
    using namespace boost::spirit::x3;  
  
    char const* const it = "";  
    auto p = with<class tag>(42)[  
                 print_tag_value  
              >> with<class tag>(123)[print_tag_value]  
              >> print_tag_value];  
    parse(it, it, p);  
}  
```

---

## Comment 14

> Username: saki7  
> Created at: 2025-05-09 14:36:59 UTC  
> Url: https://github.com/boostorg/spirit/issues/575#issuecomment-2866795055  

Wow, I didn’t expect to run into an issue I reported myself, back when I wasn’t even the maintainer 😄   
Closing this as per https://github.com/boostorg/spirit/pull/237#issuecomment-2866781418.
