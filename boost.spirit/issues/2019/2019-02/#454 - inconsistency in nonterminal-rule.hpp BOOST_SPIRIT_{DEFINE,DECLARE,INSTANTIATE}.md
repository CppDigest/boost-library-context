# #454 - inconsistency in nonterminal/rule.hpp BOOST_SPIRIT_{DEFINE,DECLARE,INSTANTIATE} [Closed]

> Username: cppljevans  
> Created at: 2019-02-07 17:32:25 UTC  
> Updated at: 2019-02-09 13:56:14 UTC  
> Closed at: 2019-02-09 11:57:54 UTC  
> Url: https://github.com/boostorg/spirit/issues/454  

OOPS.  See next comment.

---

## Comment 1

> Username: cppljevans  
> Created at: 2019-02-07 17:38:50 UTC  
> Url: https://github.com/boostorg/spirit/issues/454#issuecomment-461525339  

Why do the _DEFINE and _DECLARE have an Attribute template instead of just using rule::attribute_type& as in the INSTANTIATE macro here:  
https://github.com/boostorg/spirit/blob/master/include/boost/spirit/home/x3/nonterminal/rule.hpp#L187  
?

---

## Comment 2

> Username: Kojoley  
> Created at: 2019-02-07 17:45:19 UTC  
> Url: https://github.com/boostorg/spirit/issues/454#issuecomment-461527610  

Because rules are kinda broken, there is no transformation before `parse_rule`, it happens after because needed for rule_definition (to allow `parse(..., rule<...>{} = something, attr)` to work). This cases lots of problems, and fixing most likely will require rule redesigning.

---

## Comment 3

> Username: cppljevans  
> Created at: 2019-02-07 17:54:04 UTC  
> Updated at: 2019-02-07 17:59:43 UTC  
> Url: https://github.com/boostorg/spirit/issues/454#issuecomment-461530750  

> Because rules are kinda broken, there is no transformation before `parse_rule`, it happens after because needed for rule_definition (to allow `parse(..., rule<...>{} = something, attr)` to work). This cases lots of problems, and fixing most likely will require rule redesigning.  
  
OK.  Hmm... Now I remember, what about this redesign:  
  
https://github.com/boostorg/spirit/pull/347  
  
Would that solve or alleviate the problem?  
  
Seems you even replied to it but only about COPYRIGHT'ing:  
  
https://github.com/boostorg/spirit/pull/347#discussion_r159740310  
  
  
Hmm... Now I'm remembering the redesign was hard to explain and pretty  
complicated, and so maybe it could use a little (or lot) of tweaking :(

---

## Comment 4

> Username: Kojoley  
> Created at: 2019-02-07 17:59:58 UTC  
> Url: https://github.com/boostorg/spirit/issues/454#issuecomment-461532794  

I remember your PR but I do not understand what it does, there are lots of unrelated changes I need to dig through.

---

## Comment 5

> Username: Kojoley  
> Created at: 2019-02-07 18:18:50 UTC  
> Url: https://github.com/boostorg/spirit/issues/454#issuecomment-461539355  

The thing is that currently rules do not synthesize attributes, however to match `parse_rule` signature it needs to - this would change the behavior and in some cases is not practical (though in my opinion it is what users opt in by using rules).  
  
Adding transformation before `parse_rule` should be easy and should not require downstream changes (downstream transformation in this case must just pass-through attribute already). But I am speculating now, cannot say for sure without trying.

---

## Comment 6

> Username: cppljevans  
> Created at: 2019-02-07 18:30:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/454#issuecomment-461543460  

> I remember your PR but I do not understand what it does, there are lots of unrelated changes I need to dig through.  
  
You're probably remembering https://github.com/boostorg/spirit/pull/342  
  
which was closed with this comment:  
  
https://github.com/boostorg/spirit/pull/342#discussion_r159009833  
  
and I proceeded with the PR 347 with only related changes, AFAICT and a clearly stated purpose  
and method of solution as 1st comment:   
  
https://github.com/boostorg/spirit/pull/347#issue-160917016

---

## Comment 7

> Username: Kojoley  
> Created at: 2019-02-07 19:06:13 UTC  
> Url: https://github.com/boostorg/spirit/issues/454#issuecomment-461555968  

> You're probably remembering #342  
  
I remember this too  
  
> with only related changes  
  
There are changes to formatting: replacing typedefs with using, whitespace changes, comments moving without need, template parameters renaming - I do not think they are all related.  
  
I am sorry, but you PR must have other problem because of recently uncovered bugs in make_attribute/post_transform. Try to run ce7647d44db9ccc6e9fa031d0cb73a4436185920 tests.

---

## Comment 8

> Username: Kojoley  
> Created at: 2019-02-07 19:17:33 UTC  
> Updated at: 2019-02-07 22:55:46 UTC  
> Url: https://github.com/boostorg/spirit/issues/454#issuecomment-461559772  

Also it seems that after #347 the debug will be called twice.  
  
I should say sorry that your PR stayed without a review, problems with rule were not on my priority list, but the main reason is that you did not provide a short test for the problem.  
  
I have a patch that solves the problem without big changes (https://github.com/Kojoley/spirit/commit/286af1e4b24c5bdee285a9ff29e7970d154df508), I will prepare a PR after dealing with #453.

---

## Comment 9

> Username: cppljevans  
> Created at: 2019-02-08 01:02:41 UTC  
> Updated at: 2019-02-08 04:32:50 UTC  
> Url: https://github.com/boostorg/spirit/issues/454#issuecomment-461654074  

> Also it seems that after #347 the debug will be called twice.  
>   
> I should say sorry that your PR stayed without a review, problems with rule were not on my priority list, but the main reason is that you did not provide a short test for the problem.  
>   
> I have a patch that solves the problem without big changes ([Kojoley@fa0483f](https://github.com/Kojoley/spirit/commit/fa0483fd49231f1d3fba51ff6cdf4022f0401f31)), I will prepare a PR after dealing with #453.  
  
Just FYI, after cloning your repository and doing:  
```bash  
evansl@lje-DL:~/prog_dev/boost/releases/ro/boost_1_69_0/sandbox/kojoley/Kololey-spirit$ git checkout --track origin/x3-perform-transformation-before-parse_rule  
Branch 'x3-perform-transformation-before-parse_rule' set up to track remote branch 'x3-perform-transformation-before-parse_rule' from 'origin'.  
Switched to a new branch 'x3-perform-transformation-before-parse_rule'  
evansl@lje-DL:~/prog_dev/boost/releases/ro/boost_1_69_0/sandbox/kojoley/Kololey-spirit$   
```  
Tried testing, but got errors.  
  
{snip}  
  
OOPS.  Checked the error msg and they were showing not the x3-perform-transformation-before-parse_rule branch.  
  
I'll try to figure what I did wrong :(  
  
I'd assumed that b2 would look where the branch was located, but that was wrong.  
Had to modify user-config.jam compiler flags to get proper includes ;(  
  
Now the `b2 x3_rule_separate_tu` test runs without error :)  
  
Also, code from here:  
  
https://github.com/boostorg/spirit/issues/453#issue-407482384  
  
compiles OK.

---

## Comment 10

> Username: cppljevans  
> Created at: 2019-02-08 08:20:07 UTC  
> Updated at: 2019-02-08 21:00:21 UTC  
> Url: https://github.com/boostorg/spirit/issues/454#issuecomment-461725161  

On 2/7/19 1:17 PM, Nikita Kniazev wrote:  
  
> Also it seems that after #347 the debug will be called  
> twice.  
  
> I should say sorry that your PR stayed without a review,  
> problems with rule were not on my priority list, but the  
> main reason is that you did not provide a short test for  
> the problem.  
  
The real reason is the tests were passing with the recent,  
at that time, changes in the attribute handling by someone  
else; hence, there was no test I knew of to show the problem  
and I guessed the only way to figure out how to test was to  
understand the attribute handling better.  I gave up since  
it looked too hard and had more pressing matters.  
  
Another reason was simple print traces showed the attribute  
transform was being done redundantly.  The only way to show  
that was to put traces in the code which would, of course,  
have to be turned off in production or removed in  
production.  Of course if they were removed, then, the test  
wouldn't show anything.  
  
Anyway, that last problem, about redundant transformations  
may still be there.  Let me outline the problem in pseudo  
code (where indentation implies call hierarchy):  
  
  
```  
  rule::parse:  
    pre_transform;  
    parse_rule:  
      rule_def.parse:  
        call_rule_definition:  
          pre_transform;//redundant?  
          parse_rhs;  
          post_transform;//redundant?  
    post_transform;//redundant?  
```   
  
I'm pretty sure the reason for the transforms in  
`call_rule_definition` is so that `rule_def` can be used on the  
rhs of a grammar expression without the need for the  
`rule` itself being on the rhs, saving some compute time.  In  
this case, the `call_rule_definition` `{pre,post}_transform` are  
*not* redundant.  
  
Of course the 2 transforms back to back may be harmless  
**most of the time**, but who knows and it seems there ought  
to be a way to avoid it.  
  
{snip}

---

## Comment 11

> Username: cppljevans  
> Created at: 2019-02-09 13:56:14 UTC  
> Url: https://github.com/boostorg/spirit/issues/454#issuecomment-462046598  

On 2/8/19 2:20 AM, cppljevans wrote:  
  
{snip-till-redundancy-transform-comment}  
> Anyway, that last problem, about redundant transformations  
> may still be there.  Let me outline the problem in pseudo  
> code (where indentation implies call hierarchy):  
>   
>   
> ```  
>    rule::parse:  
>      pre_transform;  
>      parse_rule:  
>        rule_def.parse:  
>          call_rule_definition:  
>            pre_transform;//redundant?  
>            parse_rhs;  
>            post_transform;//redundant?  
>      post_transform;//redundant?  
> ```  
  
{snip}  
  
> Of course the 2 transforms back to back may be harmless  
> **most of the time**, but who knows and it seems there ought  
> to be a way to avoid it.  
  
Of course reading [my own  
PR comments](https://github.com/boostorg/spirit/pull/347#issue-160917016)  
made back then might have helped answer that question of how  
to avoid the redundancy:  
  
> The solution is to have this generated `parse_rule` call  
> instead the new function  
> `rule_definition<...>::parse_no_xform` which does no  
> attribute transform.  
  
Those comments are echoed by the in-source comments  
[here](https://github.com/cppljevans/spirit-experiments/blob/150d1d024570e16ea17a38c867c48d529bfc3fd3/include/boost/spirit/home/x3/nonterminal/rule.hpp#L56)  
and I'd guess elsewhere.  
  
So, **NOTE TO SELF**, read the docs first before attempting  
to understand something ;(
