# #21 Fix template arguments (Mapped type was missing) and add support for the boost unordered types [Closed]

> Username: fiesh  
> Created at: 2015-08-20 15:30:13 UTC  
> Updated at: 2015-10-07 19:05:29 UTC  
> Closed at: 2015-10-07 18:25:53 UTC  
> Url: https://github.com/boostorg/serialization/pull/21  

The code definitely a lot of copy & paste since I didn't want to break any compilers or anything --- one could probably use enable_if to make things a little bit cleaner.

---

## Comment 1

> Username: robertramey  
> Created_at: 2015-09-28 05:43:59 UTC  
> Url: https://github.com/boostorg/serialization/pull/21#issuecomment-143645314  

I believe this functionality is already in the library since version 1.59 released april 2015

---

## Comment 2

> Username: fiesh  
> Created_at: 2015-09-28 12:29:10 UTC  
> Url: https://github.com/boostorg/serialization/pull/21#issuecomment-143729019  

I don't think this is true or fail to use it correctly then.  The unordered_\* haven't changed from 1.58 to 1.59.

---

## Comment 3

> Username: robertramey  
> Created_at: 2015-09-29 06:18:04 UTC  
> Url: https://github.com/boostorg/serialization/pull/21#issuecomment-143956842  

OK - I see it now - this is for boost/unordered.hpp - which I didn't even know existed!  
  
A couple of observations:  
  
a) My policy has been to maintain serialization of standard library types.  In this case this meant hash and unordered.  This policy would mean that I would leave the serialization of boost/unordered in the hands of that author.  
  
b) On the other hand, boost/unordered is the same (as far as I can tell) has hash and unordered so ....  
  
c) I certainly didn't like mixing serialization of boost/unordered in with the same file as std::unordered. It confused me a lot - especially since I didn't know boost/unordered didn't exist.  
  
d) I didn't like the extensive cut and paste. This results in a lot more code to maintain.  Being the maintainer I don't like this.  On the other hand, I now see that's what I did myself - but keeping the code separate files made is simpler to keep track of this.  Examining the changes in more detail, I realize now the implementation should have a template template parameter so that there would be only one implementation for hash, std::unordered and boost::unordered.  Examining your files and comparing the current ones suggests this is possible and convenient.  This would actually diminish code to be maintained.  So I'm convinced this is the way to go.  
  
e) why not make a track item for this so I don't forget.  I believe that adding the template/template parameter in the right places wouldn't be too hard but still would take some care.  Also the tests should be re-factored (again!) to exploit this.  Right now I'm bogged down in other stuff so I won't get to it right  away.  
  
Robert Ramey

---

## Comment 4

> Username: fiesh  
> Created_at: 2015-09-29 07:30:53 UTC  
> Updated_at: 2015-09-29 07:39:31 UTC  
> Url: https://github.com/boostorg/serialization/pull/21#issuecomment-143972929  

Thanks for your reply!  
  
a) The boost::unordered types became the tr1 unordered types that led to the std::unordered types.  The earliest patch for serializing these types (see https://svn.boost.org/trac/boost/ticket/5265) was against the tr1 ones.  In fact, your change from tr1 to std resulted in the boost-types no longer being serializable.  So in this case, I am not certain if the policy of having other projects serialize their types is the best way to go here.  
  
(Side node: One might be confused about what the point could be in still using the boost types instead of the std ones now.  From my experience, the boost types behave much nicer in a couple ways, performance and, most importantly, convenience.  The boost/functional/hashing function they use is very nicely implemented and works out of the box for many types that the std::hash function does not and requires manual combining of hashs and so on.  So after a while of getting frustrated with std::unordered, I switched back to boost::unordered and was much happier again.  EDIT: I just remembered std::unordered also isn't required to work with incomplete types, while boost::unordered does, another nuisance in the std version.)  
  
d) I agree with the design choice completely.  I did this copy & paste style because I didn't want to introduce any subtle problems with some compilers I couldn't test and so on.  
  
e) I'll do that, but I figured github was the way to go with the boost development now.  It is here: https://svn.boost.org/trac/boost/ticket/11689  
  
Are you saying you want to look into refactoring the patch yourself?

---

## Comment 5

> Username: robertramey  
> Created_at: 2015-09-29 15:34:27 UTC  
> Url: https://github.com/boostorg/serialization/pull/21#issuecomment-144095960  

> On Sep 29, 2015, at 12:30 AM, fiesh notifications@github.com wrote:  
>   
> Thanks for your reply!  
>   
> a) The boost::unordered types became the tr1 unordered types that led to the std::unordered types. The earliest patch for serializing these types (see https://svn.boost.org/trac/boost/ticket/5265 https://svn.boost.org/trac/boost/ticket/5265) was against the tr1 ones. In fact, your change from tr1 to std resulted in the boost-types no longer being serializable. So in this case, I am not certain if the policy of having other projects serialize their types is the best way to go here.  
>   
> (Side node: One might be confused about what the point could be in still using the boost types instead of the std ones now. From my experience, the boost types behave much nicer in a couple ways, performance and, most importantly, convenience. The boost/functional/hashing function they use is very nicely implemented and works out of the box for many types that the std::hash function does not and requires manual combining of hashs and so on. So after a while of getting frustrated with std::unordered, I switched back to boost::unordered and was much happier again.)  
>   
> d) I agree with the design choice completely. I did this copy & paste style because I didn't want to introduce any subtle problems with some compilers I couldn't test and so on.  
>   
> Of course I understand this.  After all - that’s what I did when I created unordered.  I don’t even remember doing it.  I compared my hashed and underordered and now boost:unordered to they seem identical.  So that is the basis of my suggestion to generalize the implementation rather than copying it.  
> e) I'll do that, but I figured github was the way to go with the boost development now. It is here: https://svn.boost.org/trac/boost/ticket/11689 https://svn.boost.org/trac/boost/ticket/11689  
> Are you saying you want to look into refactoring the patch yourself  
>   
> If you want to do this, that would be great!.  Feel free to fix it up and resubmit.  
  
Just prove again that no good deed goes unpunished, I’ve looked at the implementation serialization of std::map  
and found that it’s actually compatible with hashed and unordered versions  
  
What I would like to see is all this code consolidated so that functionality is shared and not repeated.  
  
This would likely require usage of template template parameters  
  
a) Some specializations  
b)This would entail  
  
This might end up looking like:  
a) separate files for boost/seriliazation/map.hpp   
boost/serialization/map.hpp // std::map  
boost/serialization/unordered_map.hpp // boost::unordered_map  
boost/serialization/unordered_map.hpp // boost::unordered_map  
or  
one file with the three implementations.   
  
Personally I generally prefer the former.  I find it easier to setup up finer grain testing  
and also tends to diminish code bloat.  
  
But I could be wrong as these files would likely be forwarding  
calls to the base implementation templates which would then be in a separate file.  
  
Again, thanks for looking into this.  If want to finish this job I’ll take another look.  I’m  
also available for questions about this.  The net result would be to shrink the code base  
which I’m always in favor of.    
  
Robert Ramey  
Robert Ramey Software Development  
(805)569-3793  
ramey@rrsd.com  
www.rrsd.com  
  
PS, if you make these changes, be sure to add your name to the copyright information  
so we know who blame if things go wrong.  
  
RR

---

## Comment 6

> Username: fiesh  
> Created_at: 2015-10-04 08:59:22 UTC  
> Url: https://github.com/boostorg/serialization/pull/21#issuecomment-145328883  

Heh I just got an email saying that you definitely beat me to it!  Sorry, had no idea you'd be that quick, I was going to work on it soon but didn't manage until now.

---

## Comment 7

> Username: robertramey  
> Created_at: 2015-10-04 16:04:23 UTC  
> Url: https://github.com/boostorg/serialization/pull/21#issuecomment-145362996  

Turns out that this week I spent clearing a number of issues with the library - I've still got a couple more.  I spend long periods of not messing with it and it takes me almost a day to get back up to speed about how it works.  So it's more efficient to do a bunch of them at a time.  Your work wasn't wasted, without your doing it, I never would have done the final version.  Your version wasn't incorrect, I just spent a little time getting re-factoring so that it has less cut and paste code.  Other than that it's the same as yours.  Cut & pasting code is attractive - but longer term it's much harder to maintain.  
  
One thing that came up is that for the first time I had to look into set/map C++ documentation in a more exhaustive manner.  I'm thinking that there are opportunities for performance improvements by permitting implicit move semantics.  If I had more time, which I don't, I'd be inclined to spend it on looking into this.  You might want to do this as  hobby.  It's definitely much more interesting than implementing and testing this fix.  
  
I've got a couple more fixes I want address in this "session" and I also want to move portable binary archive to a first class supported archive.  
  
Maintaining the serialization library can be a very big job.  Fortunately, for me it has been OK, mainly because I'm somewhat conservative in testing, etc.  Maintaining this library has an extra burden in that not only does one need/want to maintain compatibility with older compilers but also with previously created datasets - going back 15 years!!!!.    This imposes a higher standard than maintaining other libraries require.  
  
Efforts from people such as yourself have in most cases been very helpful.  Even in cases where it doesn't seem helpful, it shows what problems people are having.  The serialization library is very much "demand driven" in that most of the enhancements/fixes are driven by people who have specific problems which motivates me to provide a solution.  In crafting a solution, I try to make it more general.  Over the years it has made the library much more widely useful to the extent that it would seem a difficult task for someone to replace in toto.  So I guess I'll have a job at least for another 15 years (that is until i die).  I just wish it paid better.

---

## Comment 8

> Username: fiesh  
> Created_at: 2015-10-07 18:25:53 UTC  
> Url: https://github.com/boostorg/serialization/pull/21#issuecomment-146285197  

I think I speak for a lot of people when I say your serialization library is highly appreciated.  It requiring maintenance after 15 years shows its success and speaks for itself.  
  
An attempt at constructive criticism: You might want to try using github more, especially being able to merge pull requests directly, and not resort so much to hand-reworking.  I'm not talking about this one, since I understand you wanted it re-factored, but I think using github, its ticket system and the pull request functionality surpasses the methods used so far and might make your life easier.  
  
Thanks again for the great library!

---

## Comment 9

> Username: robertramey  
> Created_at: 2015-10-07 19:05:29 UTC  
> Url: https://github.com/boostorg/serialization/pull/21#issuecomment-146296505  

Very rarely can I just merge in a pull request.  It's apparent that most of them aren't even tested by the authors!!!.  Then there is the fact that many/most are the master branch !!!.  Finally, there is the issue of quality.  Yours is typical.  Not that it's bad per se, it was expedient in that in enhancing the library (or correcting a blunder) it makes the volume of the code larger.  Lot's of cut and paste code makes the library unmaintainable.  The "hand reworking" in your case resulted in more capability wit less code to maintain.  
  
Also the TRAC system actually is much better for me - it keeps all the issues in one place.  Pull requests end up all over the the place.  And I'm not sure what happens when a pull request is closed.  Do they disappear for ever? Where does one find the old ones.  The TRAC system works much better for me.  
  
BTW - the reason it's around for 15 years (and likely another 15 at least) is because it's been maintained - not the reverse.  Of course the fact that it's widely used motivates one to continue maintaining it. it's a virtuous cycle.

---
