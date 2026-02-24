# #262 - Logical expression for test labels [Open]

> Username: mxp1983  
> Created at: 2020-03-30 09:55:36 UTC  
> Updated at: 2022-07-18 11:59:47 UTC  
> Url: https://github.com/boostorg/test/issues/262  

It would be nice to be able to make an AND operation between labels.  
Imagine following:  
Each test case has:  
* a label which specifies test case type - there are also other labels like "regression", "load", "stress" and so on.  
* a label which specifies environment on which the test case is running: "linux", "windows", "RaspberryPi" and so on  
  
The point is that with AND condition, I can easily filter test cases, for example:   
`@regression&@linux`  
  
If there is no such feature currently (which I missed) and if the syntax is OK for you, then I could prepare a proposal - maybe it would speed up including it into boost 1.73 :)

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2020-03-30 16:08:00 UTC  
> Updated at: 2020-03-30 16:16:52 UTC  
> Url: https://github.com/boostorg/test/issues/262#issuecomment-606092544  

This is a great idea! Also some users wanted something similar quite a long time ago: https://svn.boost.org/trac10/ticket/12902 so if we find a solution concerning the syntax, it should also work for that request.  
  
I believe the `&` or `|` might be problematic as they interact with some shells. For `OR`, we can repeat the argument on the command line or maybe use `,` as for the unit test names filter.  
For `AND`, what about just repeating with `@` ?   
  
This would be   
  
* `@regression,linux` I am looking for any label `regression` or `linux`  
* `@regression@linux` I am looking for label `linux` inside the previously filtered label `@regression`.  
  
It is not top notch, so if you have a better proposal, I am all open to suggestions.

---

## Comment 2

> Username: mxp1983  
> Created at: 2020-03-30 18:50:42 UTC  
> Url: https://github.com/boostorg/test/issues/262#issuecomment-606177139  

You've mentioned that some shells may have issues with & or |. Do you think that adding --run-test expression in "" would help? For example:   
`--run_test="@regression & @linux"` <- we could maybe add also spaces to increase readability. If "" are ok but '&' is problematic, then I would exchange it with AND/and.  
  
I will try to implement some suggestion and then you can check if you like it. Assuming that putting the expression in "" would help, I will try something like:  
`--run_test="tcp_suite/server/* & @linux & @regression`  
This would cover the ticket 12902 you've mentioned.  
  
Give me a week or two to play around and see what I can do.

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2020-03-30 20:19:30 UTC  
> Url: https://github.com/boostorg/test/issues/262#issuecomment-606225442  

That would be wonderful, thanks!

---

## Comment 4

> Username: mxp1983  
> Created at: 2020-04-01 19:04:50 UTC  
> Updated at: 2020-04-01 19:06:50 UTC  
> Url: https://github.com/boostorg/test/issues/262#issuecomment-607435663  

Hi,  
  
just an update about my progress:  
I have chosen the solution, which is limiting changes in the current code: the expressions are within (), thus I extended only add_filtered_test_units with one 'else if'.  
  
Of course, brackets can be also used for grouping and they can be also nested, so this kind of expression is possible:  
`--run_test=((ts2/ts1/* & @L2), @f3)`  
It means: run all tests from ts2/ts1/* with label `@L2` and tests with labels `@f3`.  
There is no limit of sub-expressions / nested sub-expressions.  
  
Basically there are two operators:  
* "&" - set intersection  
* ","  - set union  
  
Ah, negation works too:  
`--run_test=((ts2/ts1/* & @L1) & !@L2)`  
So: run all tests from ts2/ts1/* with label `@L1` and exclude those having label `@L2`.  
  
I'm currently writing test cases to cover different use cases.   
I think that in few days I will be ready for the code review.

---

## Comment 5

> Username: raffienficiaud  
> Created at: 2020-04-01 20:41:21 UTC  
> Url: https://github.com/boostorg/test/issues/262#issuecomment-607479066  

Glad to hear that, thanks for all this work!

---

## Comment 6

> Username: mxp1983  
> Created at: 2020-04-02 19:08:36 UTC  
> Url: https://github.com/boostorg/test/issues/262#issuecomment-608049635  

Ok, I have some test cases and also some changes:  
* operator ',' is changed to ';' - to not confuse people with the already existing one.  
* new operator '!' - inversion  
* new file/class test_case_collector - similar to test_case_counter but holds also names of test cases - used for testing  
* Operators are executed from left to right (no precedence) and grouping should be done via putting the expression in ().  
  
I have written the following test cases:  
* test_run_by_expression_compatibility_1 - the same as test_run_by_label but uses expressions  
* test_run_by_expression_compatibility_2 - almost the same as test_run_by_name but uses expressions  
* test_run_by_expression_label - checks different expressions with nested expressions and usage of all available operators. Additionally checks error handling (missing bracket, too many consecutive operators and so on)  
  
You can find the code in my fork of boost - branch: topic/GH-262-filtering-operators:  
https://github.com/maksymilian-palka/test/tree/topic/GH-262-filtering-operators  
  
I hope you will like new possibilities for filtering.

---

## Comment 7

> Username: raffienficiaud  
> Created at: 2020-04-02 20:26:33 UTC  
> Url: https://github.com/boostorg/test/issues/262#issuecomment-608082005  

Excellent! So now you know everything about Boost.Test internals! There is this `static` variable that I believe should be removed.  
  
I am looking at the code and at an alternative syntax, in particular how we can reuse the current syntax to make it work in your new scenario. The rationale is that we need several new operators (parenthesis, `&` and `;`) and they depend on the context, which is difficult to explain to users.  
  
So we have already the union from a `,`, and in fact we have already the intersection with `/` (but applied only to test units):  
  
* `s1/s2` means `s2` inside `s1`, which is set `s1/s2` intersecting `s1/`  
* `s1,s2` means `s1` or `s2`.  
  
What about something like this:  
  
* `s1/s2/@L1,@L2` means inside `s1/s2` labels `L1` or `L2`.  
* `s1/s2/@L1/@L2` means inside `s1/s2` labels `L2` inside `L1` (intersection)  
* `s1/s2/@L1/!@L2` means inside `s1/s2` labels all but `L2` inside `L1` (intersection)  
* `s1/s2/@L1,s3,@L2` inside `s1/s2` labels `L1`or `L2` (union) or `s3`  
  
Here we identify two parts:  
  
* subtree selection as before  
* after each `/` we can have a label (new) or a test unit filter, possibly with negation `!` (new)  
* we can compose labels with `/` (new)  
* we have `,` as before, each of the elements are union composed relative to the parent subtree selection: in `s1/s2/@L1,s3,@L2` the `@L1` is relative to `s1/s2`, same for `s3` and `@L1`  
* we cannot compose labels together with test units on its right: `@L2/s3` cannot be done. We can do the other way round: `s3/@L2` (which means basically the same)  
* label composition is commutative: `@L1/@L2` is equivalent to `@L2/@L1`, just like a regular `AND`  
* we will not allow something like `s1/s2/@L1,s3/s4,@L2`, because otherwise we may want to compose further inside `s1/s2/s3/s4` with labels, which forces the introduction of parenthesis.  
  
This has less expressive power that your proposed syntax, but it may be easier to explain. What do you think?

---

## Comment 8

> Username: mxp1983  
> Created at: 2020-04-02 22:29:49 UTC  
> Url: https://github.com/boostorg/test/issues/262#issuecomment-608124895  

Regarding the static variable - I needed a way to have a list of all test cases and I didn't want to recreate it every time within the function (which is called recursively).  
  
To justify my current approach: I've chosen it to not touch the existing code and to keep 100% compatibility with the current syntax.  
  
Regarding the syntax, I have mixed feelings. From one side I understand the will of expressing new things with the old syntax, but from another side, I'm pretty sure that it may lead to unreadable and complicated syntax, which will not scale well.  
  
For example: this `s1/s2/@L1,s3,@L2` can be a little bit confusing for a user: does `@L2` apply to s1/s2/s3 or maybe s1/s2?  
Imagine now more complicated filtering without proposed expressions: the filter will become a mixture of '/', ':' and ','. Additionally, without grouping: it will be quite challenging to write a filter and then extremely challenging to decrypt, what exactly is being selected and then adapt it.  
  
You've mentioned that new syntax would be difficult to explain to the users. I think, that it is not the case. Let's assume, that ';' is replaced with ',' again; operator '!' has the same meaning as now. There is only one new operator '&' which for most developers is straightforward.   
In this case, the explanation could be like this:  
"Operator '&' intersects and operator ',' combines resulting sets of expressions on the operator's left and right side. Operator '!' inverts resulting set of the expression on the right side. The expression can be a test case/suite path or a label. The expression can be placed in "()", forming a subexpression which is evaluated first."  
After that, there can be up to 10 examples and everything should be obvious :)  
  
In my opinion, the following is a good option:  
* leaving current syntax for simple filtering  
* introducing expressions' syntax, which starts with '(', what is a nice separation. In this syntax, operators used for simple filtering are still valid. Additionally, operator '&' is available. Here I assume that ',' is used for a union. Operator '!' is already used for inversion/negation.  
* Parenthesis should be available for grouping. It gives more power and protects from future scenarios when some users need a more versatile way of filtering.  
  
Did I convince you towards extending the syntax by () and & rather than trying to express a subset of new things with the old syntax?

---

## Comment 9

> Username: mxp1983  
> Created at: 2020-04-29 15:47:58 UTC  
> Url: https://github.com/boostorg/test/issues/262#issuecomment-621298356  

So.... is the discussion over or what are the next steps?

---

## Comment 10

> Username: raffienficiaud  
> Created at: 2020-04-29 16:53:29 UTC  
> Url: https://github.com/boostorg/test/issues/262#issuecomment-621335307  

Hi, sorry no the discussion is not really over :D (I was preparing for 1.73). Give me a couple of days and I'll come back with a plan.

---

## Comment 11

> Username: mxp1983  
> Created at: 2020-07-10 12:47:00 UTC  
> Url: https://github.com/boostorg/test/issues/262#issuecomment-656658075  

Hi,  
so... what is the plan? :)  
I'm starting implementing new test project at work and I would really need advanced filtering (labels + paths) :D

---

## Comment 12

> Username: chrissbarr  
> Created at: 2022-07-18 11:59:47 UTC  
> Url: https://github.com/boostorg/test/issues/262#issuecomment-1187214502  

Did anything ever come of this? I have an application where the ability to filter on a label within a particular test suite (e.g., the intersection of path and label, as above) would be very useful.
