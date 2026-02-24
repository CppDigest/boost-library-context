# #380 X3: x3::variant fixes [Closed]

> Username: Kojoley  
> Created at: 2018-03-19 00:46:18 UTC  
> Updated at: 2018-06-28 13:22:50 UTC  
> Closed at: 2018-06-28 13:22:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/380  

Supersedes #379

---

## Comment 1

> Username: RandomInEqualities  
> Created_at: 2018-03-19 08:00:17 UTC  
> Url: https://github.com/boostorg/spirit/pull/380#issuecomment-374128711  

I feel embarrassed about the other code now, this is so much better.

---

## Comment 2

> Username: djowel  
> Created_at: 2018-03-22 22:24:02 UTC  
> Url: https://github.com/boostorg/spirit/pull/380#issuecomment-375477276  

Hi @Kojoley. Could you explain what's going on here? It's hard to follow the code from the changes. It's probably easier to ask.

---

## Comment 3

> Username: Kojoley  
> Created_at: 2018-03-22 22:28:32 UTC  
> Url: https://github.com/boostorg/spirit/pull/380#issuecomment-375478323  

To ensure that we don't have other problems with `x3::variant` I've added tests.  
Now I'm removing code bloat, because in C++14 terms `x3::variant` is an abomination.

---

## Review 4 [Commented]

> Username: djowel  
> Created_at: 2018-03-22 22:46:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/380#pullrequestreview-104823793  

> To ensure that we don't have other problems with x3::variant I've added tests.  
  
Could you explain how it does that?  
  
> Now I'm removing code bloat, because in C++14 terms x3::variant is an abomination.  
  
I agree with that. But I am not yet sure if the new code is equivalent. For example, 'explicit' was removed (you might have missed my comment on that).  
  
P.S. Please refrain from using hyperbole such as 'abomination'. These are the kind of words that work against collaboration. We can't have that here.

📁 include/boost/spirit/home/x3/support/ast/variant.hpp

```diff
 140 | 
 141 |         template <typename T, class = non_self_t<T>>
 142 |-         explicit variant(T const& rhs) BOOST_NOEXCEPT_IF((std::is_nothrow_constructible<variant_type, T const&>{}))
```

> Username: djowel  
> Created_at: 2018-03-19 01:47:35 UTC  
> Updated_at: 2018-03-22 23:41:30 UTC  
> Url: https://github.com/boostorg/spirit/pull/380#discussion_r175316142  

Why did we remove explicit?


---

## Comment 5

> Username: Kojoley  
> Created_at: 2018-03-22 23:02:57 UTC  
> Updated_at: 2018-03-22 23:14:13 UTC  
> Url: https://github.com/boostorg/spirit/pull/380#issuecomment-375485536  

> > To ensure that we don't have other problems with x3::variant I've added tests.  
> Could you explain how it does that?  
  
Ehmm.  
  
> I've added tests.  
  
For any possible operation.  
  
> > Now I'm removing code bloat, because in C++14 terms x3::variant is an abomination.  
> I agree with that. But I am not yet sure if the new code is equivalent.  
  
I did write description for every modification, that's why there are so many commits.  
  
> For example, 'explicit' was removed (you might have missed my comment on that).  
  
Copy and move initialization is a valid operation for variants. I don't know why it was disabled  
  
> (you might have missed my comment on that).  
  
I'm sorry, but I do not see them neither on email nor on web, could you post a link?  
  
> P.S. Please refrain from using hyperbole such as 'abomination'. These are the kind of words that work against collaboration. We can't have that here.  
  
I'm sorry (I'm very sorry if this word is considered rude), I'm not a native speaker, actually my English is bad. But what is the problem with using hyperboles?

---

## Comment 6

> Username: Kojoley  
> Created_at: 2018-03-22 23:37:25 UTC  
> Url: https://github.com/boostorg/spirit/pull/380#issuecomment-375492266  

Well, I've hit a GCC bug that is not fixed at least for around 6 years as for now https://gcc.gnu.org/bugzilla/show_bug.cgi?id=52869

---

## Comment 7

> Username: mjcaisse  
> Created_at: 2018-03-22 23:46:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/380#issuecomment-375493891  

Hi @Kojoley -  
  
I'll quickly address the hyperbole. It isn't the specific use of the word _abomination_ that is a problem. It is the general tone that you portray when using it. You sound rude, conceited, and make it hard to work with.  
  
If you look at the history of `variant.hpp` you will see that it was committed May 1, 2014 by Joel. I don't know of anybody that would call Joel's work an _abomination_. There are also a few things that I might know that you haven't considered. Two weeks after that initial commit, Joel and I are going to speak on X3 in a 3-hour lecture and workshop at C++Now. At that same event, X3 will be officially released for public consumption. There is a lot going on and C++14 is young.  
  
It may very well be that `x3::variant` is a horrible mess, poorly designed, and sloppily implemented. I doubt it is that bad ... but lets assume it is. A far better approach than declaring it an abomination or disaster (or any term that doesn't add value) is simply to point out where improvements can be made. Note problems and offer solutions.  
  
Somebody wrote the code you are criticizing. A variety of trade-offs are made all the time: compiler support, time, compatibility, ... . You may not understand some of the constraints or it might be that the implementation needs to be improved. By being specific about your concerns and the possible improvements that can be made, we can simply make things better.   
  
You might be really clever and smart; however, your tone constantly portrays arrogance and an air of hard-to-work-with. Joel and I have been working together for many years. I know he has no ego and simply wants to collaborate and see the libraries improve.  
  
It makes it easier for all of us to collaborate when we don't use hyperbole. The irony in me writing this note is that I love sarcasm and hyperbole! They just aren't very useful for collaboration.  
  
Thank you for the effort you put into X3. It makes a difference!

---

## Comment 8

> Username: Kojoley  
> Created_at: 2018-03-22 23:57:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/380#issuecomment-375495789  

@mjcaisse quick question here. Did you have a look at the patch?

---

## Comment 9

> Username: cppljevans  
> Created_at: 2018-03-23 00:01:44 UTC  
> Url: https://github.com/boostorg/spirit/pull/380#issuecomment-375496468  

On 03/22/2018 06:46 PM, Michael Caisse wrote:  
> Hi @Kojoley -  
>   
> I'll quickly address the hyperbole. It isn't the specific use of the word _abomination_ that is a problem. It is the general tone that you portray when using it. You sound rude, conceited, and make it hard to work with.  
>   
> If you look at the history of `variant.hpp` you will see that it was committed May 1, 2014 by Joel. I don't know of anybody that would call Joel's work an _abomination_. There are also a few things that I might know that you haven't considered. Two weeks after that initial commit, Joel and I are going to speak on X3 in a 3-hour lecture and workshop at C++Now. At that same event, X3 will be officially released for public consumption. There is a lot going on and C++14 is young.  
>   
> It may very well be that `x3::variant` is a horrible mess, poorly designed, and sloppily implemented. I doubt it is that bad ... but lets assume it is. A far better approach than declaring it an abomination or disaster (or any term that doesn't add value) is simply to point out where improvements can be made. Note problems and offer solutions.  
>   
Yes!  I'm very curious Kojoley about how you came to your conclusion.  
You might have a good point, but there no way anyone could know that  
without some hint as to why.  
> Somebody wrote the code you are criticizing. A variety of trade-offs are made all the time: compiler support, time, compatibility, ... . You may not understand some of the constraints or it might be that the implementation needs to be improved. By being specific about your concerns and the possible improvements that can be made, we can simply make things better.  
>   
> You might be really clever and smart; however, your tone constantly portrays arrogance and an air of hard-to-work-with. Joel and I have been working together for many years. I know he has no ego and simply wants to collaborate and see the libraries improve.  
>   
> It makes it easier for all of us to collaborate when we don't use hyperbole. The irony in me writing this note is that I love sarcasm and hyperbole! They just aren't very useful for collaboration.  
>   
> Thank you for the effort you put into X3. It makes a difference!  
>   
I concur there too.

---

## Comment 10

> Username: mjcaisse  
> Created_at: 2018-03-23 00:06:34 UTC  
> Url: https://github.com/boostorg/spirit/pull/380#issuecomment-375497393  

I've only been able to briefly look at the patch. My day has been swamped. There are some things that I have mentally flagged that I would like to follow up on, but I would normally do this on my own first.   
  
For example, it appears that the move constructor and move assignment have been removed. That is concerning. There are others ... and I'll probably make comments later today.

---

## Comment 11

> Username: djowel  
> Created_at: 2018-03-23 00:11:23 UTC  
> Updated_at: 2018-03-23 00:12:16 UTC  
> Url: https://github.com/boostorg/spirit/pull/380#issuecomment-375498227  

> Thank you for the effort you put into X3. It makes a difference!  
> I concur there too.  
  
I concur too. I do have some ego, but not that big :-) haha. No worries there.  
  
I think the point I am trying to make is not really about me, but for all other collaborators, in general. For example, look up the word "abomination" in the dictionary. There's nothing but negativity there:  
  
atrocity, disgrace, horror, obscenity, outrage, evil, crime, monstrosity, anathema, bane  
a feeling of hatred. detestation, loathing, hatred, aversion, antipathy, revulsion, repugnance, abhorrence, odium, execration, disgust, horror, hostility  
  
We can't have such negativity here. I too am not an english speaker. I always think twice, read twice, before I post and the dictionary is always at my side.

---

## Comment 12

> Username: Kojoley  
> Created_at: 2018-03-23 00:27:04 UTC  
> Url: https://github.com/boostorg/spirit/pull/380#issuecomment-375500630  

> For example, it appears that the move constructor and move assignment have been removed. That is concerning.  
  
https://howardhinnant.github.io/bloomberg_2016.pdf slide 30.  
  
> I think the point I am trying to make is not really about me, but for all other collaborators, in general.  
  
I'm sorry. It was not at any kind a personal or about. I wrote exactly  
  
> C++14 terms `x3::variant` is an abomination.  
  
> For example, look up the word "abomination" in the dictionary. There's nothing but negativity there:  
>   
> atrocity, disgrace, horror, obscenity, outrage, evil, crime, monstrosity, anathema, bane  
a feeling of hatred. detestation, loathing, hatred, aversion, antipathy, revulsion, repugnance, abhorrence, odium, execration, disgust, horror, hostility  
>  
> We can't have such negativity here. I too am not an english speaker. I always think twice, read twice, before I post and the dictionary always at my side.  
  
These strange interpretations do not show up on dictionary for me https://en.oxforddictionaries.com/definition/abomination

---

## Comment 13

> Username: djowel  
> Created_at: 2018-03-23 00:49:49 UTC  
> Url: https://github.com/boostorg/spirit/pull/380#issuecomment-375504037  

> For example, look up the word "abomination" in the dictionary. There's nothing but negativity there:  
>   
> atrocity, disgrace, horror, obscenity, outrage, evil, crime, monstrosity, anathema, bane  
> a feeling of hatred. detestation, loathing, hatred, aversion, antipathy, revulsion, repugnance, abhorrence, odium, execration, disgust, horror, hostility  
>   
> We can't have such negativity here. I too am not an english speaker. I always think twice, read twice, before I post and the dictionary always at my side.  
>   
> These strange interpretations do not show up on dictionary for me https://en.oxforddictionaries.com/definition/abomination  
  
OK, fair enough, but It's still negative:  
  
- A thing that causes disgust or loathing.  
- A feeling of hatred.  
  
Anyway, I never thought you meant anything personal, so let's just move on :-)   
  
Let's go back to technical discussions :-)

---

## Comment 14

> Username: cppljevans  
> Created_at: 2018-03-23 01:01:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/380#issuecomment-375505873  

On 03/22/2018 07:27 PM, Nikita Kniazev wrote:  
>> For example, it appears that the move constructor and move assignment have been removed. That is concerning.  
>   
> https://howardhinnant.github.io/bloomberg_2016.pdf slide 30.  
  
I assume you mean, in particular, the Nothing row which indicates  
the compiler generates the default move constructor and move assignment?

---

## Comment 15

> Username: mjcaisse  
> Created_at: 2018-03-23 01:04:12 UTC  
> Url: https://github.com/boostorg/spirit/pull/380#issuecomment-375506268  

I'm familiar with that slide. You will find similar slides in a few of my presentations in 2013 and 2014. Do you know the interaction differences between C++11, C++14, and C++17?  
  
I believe explicit is better. It means you thought about the problem and did something about it. If you want the default, be explicit and say that is what you want.

---

## Comment 16

> Username: cppljevans  
> Created_at: 2018-03-23 01:22:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/380#issuecomment-375509174  

On 03/22/2018 08:04 PM, Michael Caisse wrote:  
> I'm familiar with that slide. You will find similar slides in a few of my presentations in 2013 and 2014. Do you know the interaction differences between C++11, C++14, and C++17?  
  
So, I'm inferring from what you say that there are some negative  
interactions that could be avoided by explicitly defining the  
move constructor and assignment.  Is that right?  
>   
> I believe explicit is better. It means you thought about the problem and did something about it. If you want the default, be explicit and say that is what you want.  
>   
Being explicit is good; however, taking this advice to it's limit, we'd  
have to write explicitly *all* special functions instead of benefiting  
from the convenience of defaulting to the compiler generated ones.  
So, the c++ standard is encouraging bad (*not* abominable :) coding  
practices by providing default definitions of special functions?  
  
Is that your meaning?

---

## Comment 17

> Username: djowel  
> Created_at: 2018-03-23 01:53:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/380#issuecomment-375514303  

> Being explicit is good; however, taking this advice to it's limit, we'd  
> have to write explicitly *all* special functions instead of benefiting  
> from the convenience of defaulting to the compiler generated ones.  
> So, the c++ standard is encouraging bad (*not* abominable :) coding  
> practices by providing default definitions of special functions?  
>   
> Is that your meaning?  
  
For x3::variant, I'd like to require this:  
  
    void foo(my_variant_with_int const& var);  
  
    foo (my_variant_with_int(123))  
  
and disallow this:  
  
    foo(123)  
  
IMO, implicit conversion to a variant is dangerous. I just want to make sure that requirement is satisfied.

---

## Comment 18

> Username: cppljevans  
> Created_at: 2018-03-23 15:27:00 UTC  
> Url: https://github.com/boostorg/spirit/pull/380#issuecomment-375702553  

On 03/22/2018 08:53 PM, Joel de Guzman wrote:  
>> Being explicit is good; however, taking this advice to it's limit, we'd  
>> have to write explicitly *all* special functions instead of benefiting  
>> from the convenience of defaulting to the compiler generated ones.  
>> So, the c++ standard is encouraging bad (*not* abominable :) coding  
>> practices by providing default definitions of special functions?  
>>  
>> Is that your meaning?  
>   
> For x3::variant, I'd like to require this:  
>   
>      void foo(my_variant_with_int const& var);  
>   
>      foo (my_variant_with_int(123))  
>   
> and disallow this:  
>   
>      foo(123)  
>   
> IMO, implicit conversion to a variant is dangerous. I just want to make sure that requirement is satisfied.  
>   
Joel, FWIW, I've compiled both version of the foo call thru  
Kojoley's spirit patch and the 1st compiles but the other,  
the implicit conversion one, fails with an error msg  
about 'no matching function' and then 'candidate function ... not viable'.  
  
-regards,  
Larry

---

## Comment 19

> Username: cppljevans  
> Created_at: 2018-03-23 16:04:09 UTC  
> Updated_at: 2018-03-23 16:25:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/380#issuecomment-375715050  

On 03/23/2018 10:27 AM, cppljevans wrote:  
> On 03/22/2018 08:53 PM, Joel de Guzman wrote:  
[snip]  
>> For x3::variant, I'd like to require this:  
>>  
>>       void foo(my_variant_with_int const& var);  
>>  
>>       foo (my_variant_with_int(123))  
>>  
>> and disallow this:  
>>  
>>       foo(123)  
>>  
>> IMO, implicit conversion to a variant is dangerous. I just want to make sure that requirement is satisfied.  
>>  
> Joel, FWIW, I've compiled both version of the foo call thru  
> Kojoley's spirit patch and the 1st compiles but the other,  
> the implicit conversion one, fails with an error msg  
> about 'no matching function' and then 'candidate function ... not viable'.  
>   
> -regards,  
> Larry  
>   
>   
>   
OOPS.  I didn't do the -I compile flags correctly.  
After correction of the -I flags, it shows the patch  
does *not* catch the implicit conversion error.  
The compilation, source code, and evidence that  
correct -I flag used are shown in the gist:  
  
https://gist.github.com/cppljevans/b38704f22c9c92f63cf2ff03ef4c4675

---
