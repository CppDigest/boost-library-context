# #64 Doc: more on test case ordering [Merged]

> Username: akrzemi1  
> Created at: 2015-05-22 20:33:50 UTC  
> Updated at: 2015-05-26 08:56:48 UTC  
> Merged at: 2015-05-23 08:15:32 UTC  
> Closed at: 2015-05-23 08:15:32 UTC  
> Url: https://github.com/boostorg/test/pull/64  



---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2015-05-22 21:51:25 UTC  
> Url: https://github.com/boostorg/test/pull/64#discussion_r30933423  

why?

---

## Comment 2

> Username: raffienficiaud  
> Created_at: 2015-05-22 21:52:53 UTC  
> Url: https://github.com/boostorg/test/pull/64#discussion_r30933505  

I would just say that there is no order guarantied bu the framework/test runner, except otherwise specified by the dependency declaration

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2015-05-22 21:53:56 UTC  
> Url: https://github.com/boostorg/test/pull/64#discussion_r30933561  

Is this really a guaranty? personnally I do not know (so I'm not questioning your sentence but the framework)

---

## Comment 4

> Username: rogeeff  
> Created_at: 2015-05-22 22:10:00 UTC  
> Url: https://github.com/boostorg/test/pull/64#discussion_r30934479  

Yes this is true  
On May 22, 2015 5:54 PM, "Raffi Enficiaud" notifications@github.com wrote:  
  
> In doc/test_organization/managing_tests_dependencies.qbk  
> https://github.com/boostorg/test/pull/64#discussion_r30933561:  
>   
> > @@ -7,17 +7,31 @@  
> >   
> >  [section:tests_dependencies Managing test dependencies]  
> >   
> > -Dependencies among tests are managed by means of [link boost_test.tests_organization.decorators decorators].  
> > +In general, you should make sure that test cases are independent on one another.  
> > +For any two test cases `TA` and `TB`, `TB` must not take for granted that `TA` has already executed,  
> > +even if `TA` is declared before `TB` in the same translation unit.  
> > +  
> > +The only ordering-related guarantee that **UTF** makes by default is that if test cases `TA` and `TB`  
> > +are declared in the same test suite, no test case (call it `TX`) from any other test suite is executed between `TA` and `TB`,  
> > +even if the declaration of `TX` appears between the declarations of `TA` and `TB`.  
> > +In other words, all tests from a suite are executed in one go, even if the test suite namespace is opened multiple times.  
>   
> Is this really a guaranty? personnally I do not know (so I'm not  
> questioning your sentence but the framework)  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/test/pull/64/files#r30933561.

---

## Comment 5

> Username: rogeeff  
> Created_at: 2015-05-23 04:09:28 UTC  
> Url: https://github.com/boostorg/test/pull/64#discussion_r30941765  

Because test case independence is corner stone of TDD  
  
On Fri, May 22, 2015 at 5:51 PM, Raffi Enficiaud notifications@github.com  
wrote:  
  
> In doc/test_organization/managing_tests_dependencies.qbk  
> https://github.com/boostorg/test/pull/64#discussion_r30933423:  
>   
> > @@ -7,17 +7,31 @@  
> >   
> >  [section:tests_dependencies Managing test dependencies]  
> >   
> > -Dependencies among tests are managed by means of [link boost_test.tests_organization.decorators decorators].  
> > +In general, you should make sure that test cases are independent on one another.  
>   
> why?  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/test/pull/64/files#r30933423.  
  
##   
  
Gennadiy Rozental

---

## Comment 6

> Username: raffienficiaud  
> Created_at: 2015-05-23 08:16:41 UTC  
> Url: https://github.com/boostorg/test/pull/64#discussion_r30943082  

I would rather put the emphasis on the order: users should not rely on any execution order, and tests execution can be shuffled.

---

## Comment 7

> Username: akrzemi1  
> Created_at: 2015-05-25 07:45:07 UTC  
> Url: https://github.com/boostorg/test/pull/64#discussion_r30966985  

2015-05-22 23:53 GMT+02:00 Raffi Enficiaud notifications@github.com:  
  
> In doc/test_organization/managing_tests_dependencies.qbk  
> https://github.com/boostorg/test/pull/64#discussion_r30933561:  
>   
> > @@ -7,17 +7,31 @@  
> >   
> >  [section:tests_dependencies Managing test dependencies]  
> >   
> > -Dependencies among tests are managed by means of [link boost_test.tests_organization.decorators decorators].  
> > +In general, you should make sure that test cases are independent on one another.  
> > +For any two test cases `TA` and `TB`, `TB` must not take for granted that `TA` has already executed,  
> > +even if `TA` is declared before `TB` in the same translation unit.  
> > +  
> > +The only ordering-related guarantee that **UTF** makes by default is that if test cases `TA` and `TB`  
> > +are declared in the same test suite, no test case (call it `TX`) from any other test suite is executed between `TA` and `TB`,  
> > +even if the declaration of `TX` appears between the declarations of `TA` and `TB`.  
> > +In other words, all tests from a suite are executed in one go, even if the test suite namespace is opened multiple times.  
>   
> Is this really a guaranty? personnally I do not know (so I'm not  
> questioning your sentence but the framework)  
  
I think we need to guarantee that in order for the "suite entry/exit  
fixture" to make sense. This is what the entry/exit fixture guarantees:  
  
first suite setup, then all suite tests, last suite tear down. So, this  
precludes any shuffling of tests between suites (but allows shufflig within  
one suite).

---

## Comment 8

> Username: akrzemi1  
> Created_at: 2015-05-25 07:47:52 UTC  
> Url: https://github.com/boostorg/test/pull/64#discussion_r30967070  

2015-05-23 10:16 GMT+02:00 Raffi Enficiaud notifications@github.com:  
  
> In doc/test_organization/managing_tests_dependencies.qbk  
> https://github.com/boostorg/test/pull/64#discussion_r30943082:  
>   
> > @@ -7,17 +7,31 @@  
> >   
> >  [section:tests_dependencies Managing test dependencies]  
> >   
> > -Dependencies among tests are managed by means of [link boost_test.tests_organization.decorators decorators].  
> > +In general, you should make sure that test cases are independent on one another.  
>   
> I would rather put the emphasis on the order: users should not rely on any  
> execution order, and tests execution can be shuffled.  
  
Are you suggesting to only change the wording from imperative "you should  
not make the tests dependent" to a more informative "you should not rely on  
the ordering"?  
  
If so, I do not see any problem with such change.  
  
Regards,  
&rzej

---

## Comment 9

> Username: raffienficiaud  
> Created_at: 2015-05-25 08:20:22 UTC  
> Url: https://github.com/boostorg/test/pull/64#discussion_r30968211  

Yes. The link between test dependency and execution order is also not obvious.

---

## Comment 10

> Username: akrzemi1  
> Created_at: 2015-05-25 08:27:04 UTC  
> Url: https://github.com/boostorg/test/pull/64#discussion_r30968532  

2015-05-25 10:20 GMT+02:00 Raffi Enficiaud notifications@github.com:  
  
> In doc/test_organization/managing_tests_dependencies.qbk  
> https://github.com/boostorg/test/pull/64#discussion_r30968211:  
>   
> > @@ -7,17 +7,31 @@  
> >   
> >  [section:tests_dependencies Managing test dependencies]  
> >   
> > -Dependencies among tests are managed by means of [link boost_test.tests_organization.decorators decorators].  
> > +In general, you should make sure that test cases are independent on one another.  
>   
> Yes. The link between test dependency and execution order is also not  
> obvious.  
>   
> I do not understand what you are saying with this. Being ordered before  
> some other test is being dependent on it, isn't it?

---

## Comment 11

> Username: raffienficiaud  
> Created_at: 2015-05-25 08:30:30 UTC  
> Url: https://github.com/boostorg/test/pull/64#discussion_r30968692  

To me, these are orthogonal:  
- you make a test entry/exit to ensure that every test under the test suite have executed this fixture once (which is different from have executed this fixture "always")  
- the fact that we execute all test units under a test suite in one go can be considered as a minimal number of execution for this suite entry/exit fixtures, eg if the fixture one is costly. This is somehow not the case when shuffling (I guess).

---

## Comment 12

> Username: raffienficiaud  
> Created_at: 2015-05-25 08:32:06 UTC  
> Url: https://github.com/boostorg/test/pull/64#discussion_r30968769  

They can be ordered without dependency, aren't they? Dependency enforce order, the opposite is not true.

---

## Comment 13

> Username: akrzemi1  
> Created_at: 2015-05-25 08:40:09 UTC  
> Url: https://github.com/boostorg/test/pull/64#discussion_r30969107  

2015-05-25 10:32 GMT+02:00 Raffi Enficiaud notifications@github.com:  
  
> In doc/test_organization/managing_tests_dependencies.qbk  
> https://github.com/boostorg/test/pull/64#discussion_r30968769:  
>   
> > @@ -7,17 +7,31 @@  
> >   
> >  [section:tests_dependencies Managing test dependencies]  
> >   
> > -Dependencies among tests are managed by means of [link boost_test.tests_organization.decorators decorators].  
> > +In general, you should make sure that test cases are independent on one another.  
>   
> They can be ordered without dependency, aren't they? Dependency enforce  
> order, the opposite is not true.  
>   
> Ok, I think I see your point. You want to use term "dependency" to talk  
> about the union of "ordered before" and "run only if dependency  
> successful". This means that the section on ordering only should go  
> elsewhere, right?

---

## Comment 14

> Username: rogeeff  
> Created_at: 2015-05-25 08:41:23 UTC  
> Url: https://github.com/boostorg/test/pull/64#discussion_r30969153  

I did not have a chance to read through this. Here few points I'd like to  
make:  
1. Default order follows order of test unit declaration in translation  
   unit  
2. There is no promise bet ween translation units  
3. All test units within the same test suite are executed together  
4. Dependency enforces order, not the other way around  
5. random test unit execution shuffles test siblings of _all_ test  
   suites independently.  
6. Most complicated to explain to the case of dependency + random. What  
   happens is this: while analyzing the dependency we assign "rank" to all  
   siblings among test suite children. Lets say we have 2 children with rank 1  
7. tu1 and tu2 and 2  children with rank 2 - tu3 and tu4. We will ran first  
   tu1 and tu2 in random order, followed by tu3 and tu4 in random order  
  
On Mon, May 25, 2015 at 4:32 AM, Raffi Enficiaud notifications@github.com  
wrote:  
  
> In doc/test_organization/managing_tests_dependencies.qbk  
> https://github.com/boostorg/test/pull/64#discussion_r30968769:  
>   
> > @@ -7,17 +7,31 @@  
> >   
> >  [section:tests_dependencies Managing test dependencies]  
> >   
> > -Dependencies among tests are managed by means of [link boost_test.tests_organization.decorators decorators].  
> > +In general, you should make sure that test cases are independent on one another.  
>   
> They can be ordered without dependency, aren't they? Dependency enforce  
> order, the opposite is not true.  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/test/pull/64/files#r30968769.  
  
##   
  
Gennadiy Rozental

---

## Comment 15

> Username: raffienficiaud  
> Created_at: 2015-05-26 08:09:04 UTC  
> Updated_at: 2015-05-26 08:09:27 UTC  
> Url: https://github.com/boostorg/test/pull/64#discussion_r31011991  

My view is that all of those orders are implementation details. If some test should be run after another one, then the user should use depends_on decorator. If the suite level entry/exit fixture should be detailed (in terms of number of execution of the fixture itself), I think this is enough to say that the test tree is processed in a breadth first manner, which is exactly that. When I shuffle the test, I am expecting that there is no order at all, and test cases are picked randomly. If a test case is to be executed, and if I have random, I am (as a user) expecting that the execution is postponed until all parent test cases are executed (which is not the same as ordering by rank). So the algorithm should look like this:  
  
```  
while not queue.empty():  
      current_test_case = queue.pop_front()  
      if not current_test_case.all_parents_executed():  
          queue.push_back(current_test_case)  
          continue  
  
      # execute test   
```  
  
Here, with random, I am expecting that queue has been shuffled.

---

## Comment 16

> Username: akrzemi1  
> Created_at: 2015-05-26 08:42:12 UTC  
> Url: https://github.com/boostorg/test/pull/64#discussion_r31014237  

2015-05-26 10:09 GMT+02:00 Raffi Enficiaud notifications@github.com:  
  
> In doc/test_organization/managing_tests_dependencies.qbk  
> https://github.com/boostorg/test/pull/64#discussion_r31011991:  
>   
> > @@ -7,17 +7,31 @@  
> >   
> >  [section:tests_dependencies Managing test dependencies]  
> >   
> > -Dependencies among tests are managed by means of [link boost_test.tests_organization.decorators decorators].  
> > +In general, you should make sure that test cases are independent on one another.  
>   
> My view is that all of those orders are implementation details. If some  
> test should be run after another one, then the user should use depends_on  
> decorator. If the suite level entry/exit fixture should be detailed (in  
> terms of number of execution of the fixture itself), I think this is enough  
> to say that the test tree is processed in a breadth first manner, which is  
> exactly that. When I shuffle the test, I am expecting that there is no  
> order at all, and test cases are picked randomly. If a test case is to be  
> executed, and if I have random, I am (as a user) expecting that the  
> execution is postponed until all parent test cases are executed (which is  
> not the same as ordering by rank). So the algorithm should look like this:  
>   
> while not queue.empty():  
>       current_test_case = queue.pop()  
>       if not current_test_case.all_parents_executed():  
>           queue.push_back(current_test_case)  
>           continue  
>   
> ```  
>   # execute test  
> ```  
>   
> Here, with random, I am expecting that queue has been shuffled.  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/test/pull/64/files#r31011991.  
  
So, you are saying two things:  
1. This is technical detail, not suitable for the docs  
2. The shuffling algorithm should be different than what it is now.  
  
I ignore (1) for now, and for (2) I will let Gennadiy speak for this, but  
my opinion is that it is not clear why your algorithm should be preferred  
to the current one. The  current one already guarantees that (unless  
depend_on) is used no two tests TA and TB will be ordered in the  
predictable way.

---

## Comment 17

> Username: raffienficiaud  
> Created_at: 2015-05-26 08:49:11 UTC  
> Url: https://github.com/boostorg/test/pull/64#discussion_r31014678  

Mmm, I am not sure, especially when I read "we assign "rank" to all siblings among test suite children." So there is still an ordering following the tree structure, while it should not be. This is why I am also proposing leaving out the (1).

---

## Comment 18

> Username: akrzemi1  
> Created_at: 2015-05-26 08:56:48 UTC  
> Url: https://github.com/boostorg/test/pull/64#discussion_r31015149  

2015-05-26 10:49 GMT+02:00 Raffi Enficiaud notifications@github.com:  
  
> In doc/test_organization/managing_tests_dependencies.qbk  
> https://github.com/boostorg/test/pull/64#discussion_r31014678:  
>   
> > @@ -7,17 +7,31 @@  
> >   
> >  [section:tests_dependencies Managing test dependencies]  
> >   
> > -Dependencies among tests are managed by means of [link boost_test.tests_organization.decorators decorators].  
> > +In general, you should make sure that test cases are independent on one another.  
>   
> Mmm, I am not sure, especially when I read "we assign "rank" to all  
> siblings among test suite children." So there is still an ordering  
> following the tree structure, while it should not be. This is why I am also  
> proposing leaving out the (1).  
>   
> While I have not read the implementation, I am pretty sure that ranking  
> only reflects the depends_on relation and nothing more. (I.e, when you have  
> no depends_on, all ranks are same.) When I test this, the tests are  
> executed in the order that I find random enough.  
  
I need to describe the ordering somewhere in order to:  
- make it clear what --random is for  
- indicate that *depends_on affects the ordering  
  
I can move it to another place.

---
