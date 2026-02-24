# #8 [CMake] Generate cmake target that other libraries can use [Merged]

> Username: Mike-Devel  
> Created at: 2018-09-20 10:26:54 UTC  
> Updated at: 2018-09-21 16:34:23 UTC  
> Merged at: 2018-09-20 11:16:25 UTC  
> Closed at: 2018-09-20 11:16:25 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/8  

... to express their dependency on this library and retrieve any  
configuration information such as the include directory, binary  
to link to (if any), transitive dependencies, necessary compiler  
options or the required c++ standards level.

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2018-09-20 10:28:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/throw_exception/pull/8#pullrequestreview-157204560  

📁 CMakeLists.txt

```diff
  11 |+ target_include_directories(boost_throw_exception INTERFACE include)
  12 |+ 
  13 |+ target_link_libraries(boost_static_assert
```

> Username: pdimov  
> Created_at: 2018-09-20 10:28:22 UTC  
> Updated_at: 2018-09-20 10:57:21 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/8#discussion_r219111681  

Wrong library name.

> Username: Mike-Devel  
> Created_at: 2018-09-20 10:58:01 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/8#discussion_r219119071  

Urgs. Fixed


---

## Comment 2

> Username: pdimov  
> Created_at: 2018-09-20 11:17:36 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/8#issuecomment-423146156  

What's the end goal? Towards which library are we advancing? :-)

---

## Comment 3

> Username: Mike-Devel  
> Created_at: 2018-09-20 12:02:04 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/8#issuecomment-423157473  

> What's the end goal? Towards which library are we advancing? :-)  
  
In my dream world? All  
Personally, I'm currently interested in signals2, program_options, mp11, serialization, beast, circular_buffer and multiindex, but lets see, who in the dependency chain is willing to accept the cmake files.   
  
For now I'm just  picking some libraries from the lowest levels of `boostdep --module-levels` and try to gather feedback from you (the library maitainers). When config and the superporject have a proper cmake file, I'll have to put aside some time to go into "mass production" of PRs.  
  
Thanks for adding a CMake file to boost.core by the way.

---

## Comment 4

> Username: pdimov  
> Created_at: 2018-09-20 12:25:46 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/8#issuecomment-423163733  

Core was needed to run the Assert tests.  
  
FWIW, I have a PR for mp11 already, but it's more full-featured, has installation support. Haven't merged it because I'm not sure I want to support installation. :-)  
  
@glenfe was wondering whether he should add a CMakeLists to circular_buffer. It appears that the answer is yes. But without Config merged, we're stuck. :-)  
  
The `boost_test` function from Assert should probably go someplace common such as `libs/mincmake` but I'm not sure if we have a mandate from heaven for that yet.

---

## Comment 5

> Username: glenfe  
> Created_at: 2018-09-20 13:13:20 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/8#issuecomment-423178047  

@glenfe did a few already, but will do more when momentum picks up:  
* https://github.com/boostorg/circular_buffer/blob/develop/CMakeLists.txt  
* https://github.com/boostorg/concept_check/blob/develop/CMakeLists.txt  
* https://github.com/boostorg/align/blob/develop/CMakeLists.txt

---

## Comment 6

> Username: Mike-Devel  
> Created_at: 2018-09-20 15:34:33 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/8#issuecomment-423229160  

> But without Config merged, we're stuck. :-)  
  
Yeah that worries me too. It is of course possible to work around that in various ways, but in the end it is questionable if that is a healthy way forward.  
  
> The boost_test function from Assert should probably go someplace common such as libs/mincmake but I'm not sure if we have a mandate from heaven for that yet.  
  
No I'm afraid not ;)  
  
On a more serious note though: I don't want to give the impression to implement a cmake solution behind the back of the community (or parts of it) without a proper review process,. This is why I stick to those absolute minimal PRs that hopefully don't get in the way of whatever full-fledged cmake solution the boost community will finally settle on - whenever that happens. For the same reason I myself will not try to put utility functions into a common directory and will also keep the PR for the top level cmake file completely stupid.  
  
Anyway, @glenfe, @pdimov: Thank you both very much for picking this up.

---

## Comment 7

> Username: pdimov  
> Created_at: 2018-09-20 15:47:48 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/8#issuecomment-423233942  

In the meantime, there's https://github.com/pdimov/mincmake, used like in https://github.com/boostorg/assert/commit/a45445344357be8b5457397bd4086a097d0309c5.

---

## Comment 8

> Username: Mike-Devel  
> Created_at: 2018-09-20 16:02:43 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/8#issuecomment-423239128  

Nice.  
  
I should mention that winapi will not accept cmake related PRs until a full fleged, reviewed and accepted solution is available.

---

## Comment 9

> Username: glenfe  
> Created_at: 2018-09-20 16:14:31 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/8#issuecomment-423243322  

> No I'm afraid not ;)  
  
The mandate from heaven might require you to elicit a response from the Members listed [here](https://sites.google.com/a/boost.org/steering/).

---

## Comment 10

> Username: Mike-Devel  
> Created_at: 2018-09-20 17:35:02 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/8#issuecomment-423269469  

@glenfe: Considering the shitstorm that happened the last time the steering committee endorsed cmake, people would probably lynch them if they officially endorsed this ;)

---

## Comment 11

> Username: pdimov  
> Created_at: 2018-09-20 20:09:02 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/8#issuecomment-423316115  

FWIW, @steveire recommends we use lowercase `boost::` as the target namespace, to avoid confusion with the FindBoost targets and make the choice explicit. This may indeed save us headaches in the long run, and the time to decide is now.

---

## Comment 12

> Username: Mike-Devel  
> Created_at: 2018-09-21 09:24:35 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/8#issuecomment-423471547  

I'm somewhat on the fence on that.  
  
I deliberately chose to use the same naming convention as cmake does currently, because it makes it easier to switch between different providerds of boost at different platforms (sleft-compiled vs package-manager vs system-provided etc.). I see that this is not without danger if those get mixed in a single project, but  on the other hand, changing the name of the targets doesn't really solve that problem either.   
  
In any case, if you want to deviate from the naming convention that cmake currently uses, I would recommend something much more obvious than just switching from capital to lower case, because that is imho far to subtle.

---

## Comment 13

> Username: pdimov  
> Created_at: 2018-09-21 12:30:07 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/8#issuecomment-423514859  

It's not possible to switch because FindBoost doesn't have imported targets for header-only libraries.

---

## Comment 14

> Username: Mike-Devel  
> Created_at: 2018-09-21 13:23:32 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/8#issuecomment-423529377  

Well not for individual libraries (yet) but it does provide Boost::boost

---

## Comment 15

> Username: pdimov  
> Created_at: 2018-09-21 13:48:38 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/8#issuecomment-423536820  

Well the point is that projects using FindBoost will not work with our scheme anyway, and the idea of not using their convention is to make the choice of the scheme in use explicit and visible. If you see Boost::filesystem, the project uses FindBoost, if boost::filesystem - the new thing.

---

## Comment 16

> Username: Mike-Devel  
> Created_at: 2018-09-21 16:34:23 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/8#issuecomment-423596799  

> Well the point is that projects using FindBoost will not work with our scheme anyway  
  
It actually did work for me in the past. While it isn't ideal, it is not really a problem for a user to depend on an umbrella target for all the header only libraries (`Boost::boost`) and defining such an umbrella target is pretty straight forward. Also, I'd very much hope, that once  more and more boost header only libraries provide cmake targets, cmake itself will also forward those targets for consumation by the user. Of course if `boost` is whats used by likely candidates for a future, full fleged boost solution, then it makes sense to stay consistent with those solutions, althoug - as I said before - I'd personally prefer a more obvious difference between the two naming schemes rather than just a lower vs upper case.  
  
In any case - as long as it isn't signed of by the ML (which I'm doubtfull will happen anytime soon) - I believe any naming scheme will be subject to change in the future anyway.  
  
Again, I've not a strong opinion, which way to go, as long as I get something and in the end it is up to you library maintainers anyway what you are willing to accept into your libraries.

---
