# #46 bad make_array friend declaration [Closed]

> Username: aminiussi  
> Created at: 2016-10-15 16:52:23 UTC  
> Updated at: 2017-05-23 22:14:30 UTC  
> Closed at: 2017-05-23 22:14:30 UTC  
> Url: https://github.com/boostorg/serialization/pull/46  

The friend declaration introduce a function declaration of make_array with one template parameter which does not exist.  
  
There is an inline make_array declaration with two templates parameters, I'm guessing that's the one we want to use.  
Using T as a template parameter which mask an existing enclosing parameter in order to match the inline function definition names. Masking in a friend decl should not be an issue.

---

## Comment 1

> Username: aminiussi  
> Created_at: 2016-10-19 08:12:19 UTC  
> Url: https://github.com/boostorg/serialization/pull/46#issuecomment-254744302  

This is breaking the link on one Boost.MPI test, that will complains that the introduced make_array<T> cannot be found.

---

## Comment 2

> Username: aminiussi  
> Created_at: 2016-10-19 08:13:43 UTC  
> Url: https://github.com/boostorg/serialization/pull/46#issuecomment-254744601  

And what is this rake thing that "All checks have failed" is complaining about ?

---

## Comment 3

> Username: robertramey  
> Created_at: 2016-11-02 21:45:38 UTC  
> Url: https://github.com/boostorg/serialization/pull/46#issuecomment-258008740  

Good call - I'll make this change

---

## Comment 4

> Username: aminiussi  
> Created_at: 2016-11-11 17:06:29 UTC  
> Url: https://github.com/boostorg/serialization/pull/46#issuecomment-260004665  

The problematice 1 template param  friend declaration is still on master.  
A few links are  on  [master](http://www.boost.org/development/tests/master/developer/mpi.html) looking for it.  
  
Thanks

---

## Comment 5

> Username: robertramey  
> Created_at: 2016-11-11 17:17:33 UTC  
> Url: https://github.com/boostorg/serialization/pull/46#issuecomment-260007158  

the master is not open for merging so I can't do this.  I'll do it as   
soon as 1.63 is released - of course this means that the fix won't be in   
the 1.63 release.  But this is beyond my influence.  
  
Robert Ramey  
  
On 11/11/16 9:06 AM, Alain Miniussi wrote:  
  
> The problematice 1 template param friend declaration is still on master.  
> A few links are on master   
> http://www.boost.org/development/tests/master/developer/mpi.html   
> looking for it.  
>   
> Thanks  
>   
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub   
> https://github.com/boostorg/serialization/pull/46#issuecomment-260004665,   
> or mute the thread   
> https://github.com/notifications/unsubscribe-auth/AB4R3HpdKw98zMofA7aTnuP8LAeltRJWks5q9KCdgaJpZM4KXwQM.  
  
##   
  
Robert Ramey  
www.rrsd.com  
(805)569-3793

---

## Comment 6

> Username: Belcourt  
> Created_at: 2016-11-22 19:37:05 UTC  
> Url: https://github.com/boostorg/serialization/pull/46#issuecomment-262342706  

Robert, seems master is open for merging.  Can you please check that this fixes the MPI master tests and get it merged into master?

---

## Comment 7

> Username: robertramey  
> Created_at: 2016-11-22 20:25:22 UTC  
> Url: https://github.com/boostorg/serialization/pull/46#issuecomment-262355547  

Hmmm - I haven't seen any announcement to this effect.  looks to me that   
they're still fiddling with it  
  
How is the develop branch working for you?  
  
Robert Ramey  
  
On 11/22/16 11:37 AM, Noel Belcourt wrote:  
  
> Robert, seems master is open for merging. Can you please check that   
> this fixes the MPI master tests and get it merged into master?  
>   
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub   
> https://github.com/boostorg/serialization/pull/46#issuecomment-262342706,   
> or mute the thread   
> https://github.com/notifications/unsubscribe-auth/AB4R3O5IUN_vaR8QsHNEoei0hfrYLz76ks5rA0RigaJpZM4KXwQM.  
  
##   
  
Robert Ramey  
www.rrsd.com  
(805)569-3793

---

## Comment 8

> Username: Belcourt  
> Created_at: 2016-11-23 19:02:57 UTC  
> Url: https://github.com/boostorg/serialization/pull/46#issuecomment-262603456  

This patch seems to work fine on Darwin (clang-8.0)  and Linux (gcc-5.4).  I tested master libraries graph, mpi, graph_parallel, and serialization, no issues.  It's a variation of Alain's patch which was close, but caused an error.  
  
Since this fixes MPI master, and unfortunately MPI develop is in a broken state, could you please apply this fix to serialization develop, and then promptly get it into serialization master, so it's in for the release? I'd really appreciate it Robert.  
  
-    template<class S>  
-    friend const boost::serialization::array_wrapper<T> make_array( T* t, S s);  
+    template<class U, class S>  
+    friend const boost::serialization::array_wrapper<T> make_array( U* u, S s);

---

## Comment 9

> Username: Belcourt  
> Created_at: 2016-11-23 19:03:46 UTC  
> Url: https://github.com/boostorg/serialization/pull/46#issuecomment-262603677  

Well, guess my markup foo failed.  Here's the plain test patch.  
  
template<class U, class S>  
friend const boost::serialization::array_wrapper make_array( U* u, S s);

---

## Comment 10

> Username: Belcourt  
> Created_at: 2016-11-23 19:14:27 UTC  
> Url: https://github.com/boostorg/serialization/pull/46#issuecomment-262606168  

Well, never mind.  Doesn't work on Intel-17.  
  
../../../boost/serialization/vector.hpp(106): error: more than one instance of overloaded function "boost::serialization::make_array" matches the argument list:  
            function template "const boost::serialization::array_wrapper<T> boost::serialization::make_array(T *, S)"  
            function template "const boost::serialization::array_wrapper<boost::detail::parallel::global_descriptor<std::size_t={unsigned long}>> boost::serialization::make_array(U *, S)"  
            argument types are: (boost::detail::parallel::global_descriptor<std::size_t> *, std::size_t)  
          ar << boost::serialization::make_array(detail::get_data(t),t.size());

---

## Comment 11

> Username: mbrucher  
> Created_at: 2016-12-05 17:12:12 UTC  
> Url: https://github.com/boostorg/serialization/pull/46#issuecomment-264914206  

Any luck in figuring out Intel 17 issue?

---
