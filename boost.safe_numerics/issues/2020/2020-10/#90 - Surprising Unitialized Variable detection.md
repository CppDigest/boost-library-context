# #90 - Surprising Unitialized Variable detection [Open]

> Username: akrzemi1  
> Created at: 2020-10-14 13:02:16 UTC  
> Updated at: 2021-01-24 16:46:07 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/90  

The following code has UB due to reading a variable with indeterminate value:  
  
```c++  
int i; // no value assigned  
std::cout << i;  
```  
However, the following code is perfectly correct:  
  
```c++  
int i; // no value assigned  
std::cin >> i;  
std::cout << i;  
```  
  
For this reason, I would expect the following code not to throw exceptions:  
  
```c++  
namespace sn = boost::safe_numerics;  
  
using my_policy = sn::exception_policy<  
    sn::throw_exception,  
    sn::throw_exception,  
    sn::throw_exception,  
    sn::throw_exception // throw on uninitialized value  
>;  
  
template <typename T>  
using my_safe = sn::safe<T, sn::native, my_policy>;  
  
int main()  
{  
  my_safe<int> i; // this throws  
  std::cin >> i;  
}  
```  
  
This is unfortunate, because the library throws for a perfectly valid code. My natural expectation would be that the my safe int will want me when I try to *read* the indeterminate value (rather than when one is produced).  
  
With the current behavior, I am forced to put some starting value that will be discarded the moment after. I consider it a bad programming practice, despite what many teachers say, as explained in these blog posts:  
  
* [Concealing bugs](https://akrzemi1.wordpress.com/2016/12/12/concealing-bugs/),  
* [Treating symptoms instead of the cause](https://akrzemi1.wordpress.com/2018/11/22/treating-symptoms-instead-of-the-cause/).  
  
I imagine that detecting the read of indeterminate value is more difficult to implement, because you have to keep the track of the variable initialization state in a separate variable. If this is unimplementable, at mnimum, I would expect the documentation to warn me about the current, somewhat surprising behavior.

---

## Comment 1

> Username: robertramey  
> Created at: 2020-10-14 15:14:45 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/90#issuecomment-708470402  

Using the default EP would give you the behavior you expect/desire.  
  
To get the behavior you have above you would well use a "strict" exception policy.  
  
So, I've left it to the user to decide what he wants to do:  
  
a) detect errors in his current code without changing his coding practices.  
b) enforce a standard of "provably correct code".  
  
One can't have it both ways - the user has to choose his way.  
  
"My natural expectation would be that the my safe int will want me when I try to read the indeterminate value (rather than when one is produced)."  
  
I considered that - but it adds overhead on every usage of safe type.  Currently the library zero overhead for many safe types and I want to preserve that.  
  
"I would expect the documentation to warn me about the current, somewhat surprising behavior."  
  
The default exception policy gives you the behavior you expect.  So I don't know that it needs to be explained any more.  But I'm happy to consider documentation enhancements!  One can never have to much documentation?  LOL.  The safe numerics library has been roundly ridiculed for having too much documentation!  Of course this doesn't bother me as I thrive on ridicule!  
  
Interesting posts.  Feel free to suggest enhancements to the documentation which link to your posts.  
  
For what it's worth, I'm firmly in the camp of considering uninitialized variables as "code smell".  So if I had nothing else to do I'd promote the idea of doing something like the following.  
  
template<class T> safe<T> cin(){  
  T m_t;  
  std::cin >> m_t;  
  return m_t;  
}  
  
or   
  
template<class safe<T>> safe<T> cin(){  
  T m_t;  
  std::cin >> m_t;  
  return m_t;  
}  
  
or   
  
// or something like that  
  
Anyway - I think the current implementation is what you want.  Perhaps the documentation could be enhanced to make that more obvious.

---

## Comment 2

> Username: akrzemi1  
> Created at: 2020-10-14 15:58:09 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/90#issuecomment-708497916  

What I was expecting is the following behavior:  
  
```c++  
int main()  
{  
  boost::safe_numerics::safe<int> i;  // ok  
  std::cout << i << std::endl;        // throw: reading the indeterninate value  
}  
```  
  
The default policy does not give me the throw in the second line.  
  
I think it is implementable without any overhead the same way as the default deleter in `std::unique_ptr<T, D>` does not add overhead even though custom deleters can contain state. One just uses EBO tricks.  
  
It is fine if the library does not implement that. But because it is an expectation that a fairly broad group of users might had, I believe it should be indicated in the docs.

---

## Comment 3

> Username: robertramey  
> Created at: 2020-10-14 16:15:06 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/90#issuecomment-708508038  

> The default policy does not give me the throw in the second line.  
  
I know.  I would like to - but it's way too expensive to implement  
  
> I think it is implementable without any overhead the same way as the default deleter in std::unique_ptr<T, D> does not add overhead even though custom deleters can contain state. One just uses EBO tricks.  
  
That would be very interesting.  If you want to expand upon that I'd be interested.  If it were possible, i'd prefer that to enhancing the documentation.  Right now I'm sort of bogged down in other stuff.    
  
> One just uses EBO tricks.  
what are EBO tricks?

---

## Comment 4

> Username: akrzemi1  
> Created at: 2020-10-14 16:37:43 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/90#issuecomment-708521016  

EBO = "Empty Base Optimization". `std::unique_ptr<T, D>` implementations often uses it. Your type inherits from the policy (privately). It a default policy has no state, it adds nothing in terms of size due to EBO: compilers can elide that. If my fancy policy requires state, the type grows bigger, but this price is only paid by people that choose to use the fancy policy.  
  
```c++  
struct StatelessPolicy  
{  
  bool operator()(int) const { return true; }  
};  
  
struct StatefulPolicy  
{  
  int state;  
  bool operator()(int i) const { return i == state; }  
};  
  
template <typename Policy>  
struct MyType : private Policy  
{  
  int i;  
};  
  
static_assert(sizeof(MyType<StatelessPolicy>) == sizeof(int));  
static_assert(sizeof(MyType<StatefulPolicy>) == 2 * sizeof(int));  
```

---

## Comment 5

> Username: robertramey  
> Created at: 2020-11-07 17:56:07 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/90#issuecomment-723475124  

I'm begging to see how this might work.  We'll leave this open until more work is done on this.

---

## Comment 6

> Username: Rashika101  
> Created at: 2021-01-23 06:47:56 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/90#issuecomment-765878876  

Hey!!  
Are you willing to accept new contributors? I am interested into contributing to this repository so, if you could explain a bit more this feature request, it would be great.  
Thanks

---

## Comment 7

> Username: robertramey  
> Created at: 2021-01-23 18:43:14 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/90#issuecomment-766159005  

new contributors are always welcome (so far).  No one needs to be "accepted" - just contribute a PR.  Of course since there's no  concept of "acceptance", there's not guarantee that a contribution will be accepted by the principal maintainer - that is me. Note that it's not a "democratic" system.  It's not a consensus.  For better or worse it's my decision.  
  
I never actually thought about it, but when I do, it's amazing that I have found almost all contributions acceptable and merged them into the libraries I maintain.  There is almost no "snark", stupid arguments, impolite/personal discussions.  It's generally been a pleasure to review and accept submissions from "random".  When there is a problem, it's usually about issues like "conflicts with the library's stated purpose".  That is - a submission that is not necessarily of poor quality, but likely belongs in some other library.  
  
When you submit a PR, you should:  
1. work with a clone of develop branch of the library source on your machine  
2. run the whole test suite locally  
3. review and address any failures on the ci test suit that gets run when you check in your changes to your cloned repository.  
4. update documentation if necessary  
5. respect and emulate the stylistic conventions of the current code and documents.  The goal should be to provide a seamless experience for the current users along with some enhanced/corrected functionality  
6. feel free to include your name in the appropriate copyright notices in the code.  If the documentation has a place for it (I'm lazy about this), feel free to add your name to any acknowledgements listed.  
  
So the goal is to prepare and submit a PR that I can review and if I find it acceptable can do a squash/merge into the develop branch.  If all is well (which it will be if you've done the above), I will leave it as part of the develop branch and it will be merged along with any other pending changes into the release branch before the next boost release.  
  
Some miscellaneous points  
  
1. I prefer to use real names in communications, postings, etc.  It's not always possible - e.g. git hub does everything with user names and some are opaque.  But using really names pretty much eliminates the nonsense which some forums suffer from.  Not really a big problem here - people who contribute are proud of their work and their association with this and similar libraries.  
2. Please do not hijack discussions by including current nonsense regarding genders, race, or politics in general.  These comments are not helpful here and there are better places for them.  
3. I'm always open to rendering opinions on ideas for changes.  I love to talk about my stuff!  But please come prepared. Do your home work. Don't ask me to explain stuff that I've already made a big investment in explaining.  In particular, read the documentation - at least all the parts relevant to the question you want to ask.  If you read the docs then ask me about it - it may well result in an enhancement to the documentation!  So you can claim credit for contributing to the library just by asking a question!

---

## Comment 8

> Username: robertramey  
> Created at: 2021-01-23 18:51:39 UTC  
> Updated at: 2021-01-23 18:57:50 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/90#issuecomment-766160236  

" if you could explain a bit more this feature request, it would be great."   
  
It would be kind of hard to surpass Andresj's explanation.  But I'll take a shot:  
  
```c++  
int main()  
{  
  boost::safe_numerics::safe<int> i;  // ok  
  std::cout << i << std::endl;        // throw: reading the indeterninate value  
}  
```  
  
Any uninitialized variable (i above) can create UB when used.  So we should have NO undefined variables in our programs.  But  
  
1. The usage of uninitialized variables is sometimes unavoidable in C++ due to some vagaries in the language.  
2. the habit of using uninitialized variables is deeply engrained and some times too hard to "fix".  
3. dynamically keeping track of which variables have been uninitialized so that usage of them can be tracked has been deemed (by me) too hard to implement without compromising efficiency.  
4. Andrejz's suggestion address 3 above.  I would be very interested to see if this could be implemented without messing up the rest of the library.  It would make the library almost perfect for trapping UB with minimal performance hit.

---

## Comment 9

> Username: Rashika101  
> Created at: 2021-01-24 07:18:50 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/90#issuecomment-766304311  

Will you guide me more deeper??  
Where do i have to edit parts and to fix probably...?

---

## Comment 10

> Username: robertramey  
> Created at: 2021-01-24 16:46:06 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/90#issuecomment-766392274  

I can't add much more regarding this task without actually doing the task myself - which is what I don't want to do as its quite a difficult task.  
  
Actually it's not really that the task is difficult - though it is - it's that one has to know a lot before he can even understand what the task is!  This means that that one has to know more about C++, Boost C++ Libraries, Building and linking libraries and tests, Tools like b2, Make, Git(Hub), and some other stuff.  That's why working with Boost is considered advanced work.  It's not impossible, it's not even hard, it just takes quite an investment of effort to learn all one needs to know.  So this can be quite frustrating.  On the other hand, lots of people are willing to help: stack overflow, boost slack channel, boost mailing list etc.  There are good examples of usage of all these tools within the boost libraries.  There are lots of videos related to arcane aspects of C++ and Boost.  These help keeping one from getting stuck at a dead end.  
  
But there's also an upside.  Completing a difficult task that adds real value is tremendously rewarding personally.  And it's done in full view of the best C++ programmers around. It's a huge confidence builder. It can also be very helpful on one's resume when it comes time to look for a job.  (though I don't think it's ever done me any good in this area. But that might be due to my particular circumstances)  At Boost, we don't much go in for great ideas unless they are backed up with real, working solutions.  Anyone can start a project or task - it's fun and quick to do.  But very few people can actually complete these kinds of tasks and projects.  
  
So to address your question, I'd say get more familiar with the development workflow of a boost library.  
  
1. clone boost libraries from git hub.  
2. use the instructions at boost.org to build and install the library.  
3. make a small simple component - could be simple tiny library or simple tiny application, or some missing test, or something else.  get familiar with the workflow/development cycle.  
4. pick a more difficult task.  At this point you might feel confident about addressing one that no one has yet has the interest, knowledge or courage to address.  If not repeat this step.  
5. complete the task and create a PR and we'll take a serious look at it.  
6. Look into the possibility of formulating your task as a google summer of code project.  If accepted, you could actually get paid for working on this. (FWIW - something that no one has ever offered to me! :( )  
  
I see by looking at your profile, you're well on your way down this path. I didn't look at it until now so some of the above advice isn't applicable to you. I'm leaving it in for other interested parties.  
  
As to the specific task referred to Adrejz(?) above, familiarity with the following topics will be at least helpful and likely necessary.  
  
1. Abstract algebra: groups etc. sets of integers, sets of non-negative integers, etc.  The code in safe numerics is explicit in implementing these concepts.  
2. error handling in C++.  A seemingly simple topic which has been made complicated by having lots of people stick their fingers in it.  keywords: optional, outcome, error_code, exception, exception class, etc ....  
3. template meta programming.  And idea simple in principle, but with quite an opaque syntax due to the historical fact that it was accidentally graphed on to C++ during a standards meeting.  
4. C++ 14 - minimun requirement for this library.  This is mainly due to the extensive usage of constexpr functionality without which implementation of the library would not be possible.  
5. Range arithmetic.  available at compile time with constexpr.  
  
Hope  you've found this helpful.  Good luck with your quest.  Looking forward seeing what you (or anyone else) does with this.
