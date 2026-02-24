# #131 Fix singleton::is_destroyed and crash when using shared libraries [Closed]

> Username: Flamefire  
> Created at: 2018-10-18 10:12:37 UTC  
> Updated at: 2024-12-09 10:29:16 UTC  
> Closed at: 2018-11-17 19:31:34 UTC  
> Url: https://github.com/boostorg/serialization/pull/131  

This supersedes #105 with a more minimal approach based on current develop. Reason: Current develop contains a change (https://github.com/boostorg/serialization/commit/e1893dd28c408bb25352596e2f91892b3d570164), which I deemed mislead and reverted in #105 (5248dee2528b32907003042da84c5f4d650c470e). Note that the PR predates that change!     
To have a smaller changeset this revert is not included here.  
  
Additionally it includes the crash test #111 and the improved is_destroyed test #129 to show that this does indeed fix the problem. Those PRs should be merged first!  
  
The actual fix is https://github.com/boostorg/serialization/commit/c2c6e63c3e72a731e3a64afd8c3549b08f69344f which does the following:    
  
When a plain singleton `Foo` is used ("plain"=="does not inherit from `singleton<Foo>`) the class `singleton<Foo>` is never instantiated. Hence it is never destroyed. But the `is_destroyed` check on the current develop relies on the destruction of `singleton<Foo>` so the flag is set to false. As that destruction does not happen the flag stays true although `Foo` was destroyed. Accessing it (via `singleton<Foo>::get_const_instance()`) will then access invalid memory and crash (see #111)  
  
For this the indirection via the `detail::singleton_wrapper` is required, which also allows usage of `Foo` with a protected ctor, (which is recommended: One should not be able to do e.g. `Foo f;`)  
  
Note: The alternative is to force all users (`T`) to inherit from `singleton<T>` with the additional advantage that copy construction/assignment can be forbidden in `singleton<>` to avoid even more potential misuses.  
  
The 2nd fix commit removes the assertions on `is_destroyed` before it is runtime checked. Due to the runtime check the assertion is not required and unless one can truly and reliably fix the shared-library-issue the runtime check will be required to avoid the crash. Having the assertion in place might make someone assume the runtime check can be removed. Obviously the assertion **will** fail for #111 after  `is_destroyed` is fixed making it 2 reasons to remove.

---

## Comment 1

> Username: Flamefire  
> Created_at: 2018-11-07 11:38:36 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#issuecomment-436596009  

Rebased on develop and shows that both failures from #129 and #128 are fixed by this.  
  
As this PR includes the other 2 it can simply be merged instead of them or after the other 2.

---

## Review 2 [Changes requested]

> Username: jeking3  
> Created_at: 2018-11-07 13:09:24 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/serialization/pull/131#pullrequestreview-172468554  

When are all uses of singleton in this library guaranteed to be constructedc and destroyed?  
If they initialize at static construction time then we're okay.  
This implementation could still suffer from a multi-threaded static initialization race.  
I'm also concerned that the static destruction order is not understood.  Replacing assertions with conditional logic confirms this.

📁 .travis.yml

```diff
  98 |     echo "using $TOOLSET : : $TRAVIS_COMPILER ;" > ~/user-config.jam
  99 |-   - ./b2 -j 3 libs/serialization/test toolset=$TOOLSET link=${LINK:-shared}
  99 |+   - ./b2 -j 3 libs/serialization/test toolset=$TOOLSET link=${LINK:-shared} cxxflags=-fPIC cflags=-fPIC
```

> Username: jeking3  
> Created_at: 2018-11-07 12:39:32 UTC  
> Updated_at: 2018-11-07 13:09:24 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#discussion_r231488168  

I'm curious why bjam isn't doing this for you with link=shared on the given toolset?

> Username: Flamefire  
> Created_at: 2018-11-07 13:11:09 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#discussion_r231497263  

Because I need this when building static boost but link it against dynamic libs to reproduce this. For static boost bjam does not do this

> Username: robertramey  
> Created_at: 2018-11-08 16:29:06 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#discussion_r231964634  

FWIW - I never add the flags -fPIC.  I "trust" bam to "do the right thing" for the platform and configuration.  This could well be responsible for getting different test results.  In my view if these are necessary, they should be added to the the appropriate jam script for all shared libraries.  Then there is the issue of what the "link=shared" switch actually does.  It's my belief that it would link to the shared version of the C++ runtime library.  But then there are also C++ system libraries like libc++ - a different thing.  Then there are one's own shared libraries as we are using in our tests here.  So what does link=shared/static apply to?  As far as I'm concerned it's never been explained.  Oh and don't forget the run-link feature.  How should it be set in what circumstances? It goes on an on and on and on.  B2 build system injects myriad unexplained side-effects into one's code.  Worse these side-effects may vary silently from platform to platform.  It also doesn't help at all that the boost test matrix doesn't make it easy to see how these things are set for each test.  All this makes it very, very, very difficult to know that your build is what you really want.  And makes it much, much more difficult to collaborate given that there's no easy way to guarantee that differing testers are actually testing the same thing.

> Username: pdimov  
> Created_at: 2018-11-08 17:08:29 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#discussion_r231983344  

> FWIW - I never add the flags -fPIC. I "trust" bam to "do the right thing" for the platform and configuration.  
  
Unfortunately, `bjam` doesn't do the right thing when building shared libraries that incorporate static libraries. It adds `-fPIC` when building the shared library, but not when building the static library.  
  
> Then there is the issue of what the "link=shared" switch actually does. It's my belief that it would link to the shared version of the C++ runtime library.  
  
No, that's `runtime-link=shared`. `link=shared` applies to the libraries built by `b2`, makes them shared.

> Username: robertramey  
> Created_at: 2018-11-08 17:30:11 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#discussion_r231991101  

off topic - so what is runtime-link set to by default?  My understanding is that at least on windows if one module is set to use static version of the C++ runtime and another is set to use Dll  version, ceos ensues. as far as I know, there's no obstacle to one doing this.  b2 is opaque to me.  
  
and BTW - it seems that -fPIC shouldn't be necessary when building a static library as the addresses are fixed up at link time.

> Username: pdimov  
> Created_at: 2018-11-08 17:55:37 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#discussion_r231999668  

The default for `runtime-link` is `shared`. `b2` will not let you mix the two. `-fPIC` is necessary when building a static library if you later incorporate this library as part of a shared library.

> Username: Flamefire  
> Created_at: 2018-11-08 18:17:10 UTC  
> Updated_at: 2018-11-08 18:47:24 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#discussion_r232006601  

@ `-fPIC`: The crash happens when static boost is linked into shared libs. Hence:  
  
- Build a new library: https://github.com/boostorg/serialization/pull/131/files#diff-e97299ad55a4013dc19889043aa464a5R55  
- Make it a shared library: https://github.com/boostorg/serialization/pull/131/files#diff-e97299ad55a4013dc19889043aa464a5R60  
- Link against static boost: https://github.com/boostorg/serialization/pull/131/files#diff-e97299ad55a4013dc19889043aa464a5R58  
- Due to this (static lib is **consumed** by dynamic lib which is not "backpropagated" by bjam) add -fPIC

> Username: res2k  
> Created_at: 2018-11-14 08:46:54 UTC  
> Updated_at: 2018-11-14 08:47:06 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#discussion_r233355804  

I like to add `-fPIC` via a build feature, it's more "surgical" and automatic (no need to pass correct flags on bjam command line).  
See here: https://github.com/res2k/serialization/commit/b279e155bb9ba18499c38daa0dfe3a190a23390d


📁 src/extended_type_info_typeid.cpp

```diff
  98 |-         BOOST_ASSERT(! singleton<tkmap>::is_destroyed());
  98 |+         // Check that the singleton is still alive (might be unloaded already)
  99 |         if(! singleton<tkmap>::is_destroyed()){
```

> Username: jeking3  
> Created_at: 2018-11-07 12:55:29 UTC  
> Updated_at: 2018-11-07 13:09:24 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#discussion_r231492733  

Under what conditions would the singleton be "already destroyed" here?

> Username: Flamefire  
> Created_at: 2018-11-07 13:16:54 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#discussion_r231498983  

See #128:  
  
- Assume 2 libraries using `extended_type_info_typeid_0` (singletons) with different types. Lets call these singletons `type<Foo>` and `type<Bar>`  
- On initialization time both types register themselves in `singleton<tkmap>`, which hence gets constructed before.  
  - Note that each library has its own instance of `singleton<tkmap>` due to static linkage of boost  
- On destruction time library A gets unloaded: `type<Foo>` gets destructed, unregisters itself in `singleton<tkmap>`, afterwards `singleton<tkmap>` gets destructed.  
- Due to those destruction issue in shared libs **both** `singleton<tkmap>` get destructed (you can check this easily with PRINTF in #128)  
- Now when `type<Bar>` gets destroyed the `singleton<tkmap>` it wants to access is already destroyed  
  - Previously this caused a BOOM -> See #128  
  - Now the assertion would fire and the runtime check catches this.  
  - No unregistering is required as the register does not exist altogether.  
  - All good, just skip the unregistering code

> Username: robertramey  
> Created_at: 2018-11-08 16:38:29 UTC  
> Updated_at: 2018-11-08 16:38:30 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#discussion_r231971212  

I expect that most singletons used in the serialization library to be constructed pre-main time and so in these cases I would expect is_destroyed to always return false.  BUT questions arise regarding this for dynamically loaded shared libraries.  Each shared library probably has it's own set of singletons.  Since shared libraries are not addressed by the standard and behavior is not documented anywhere that I know of, I don't know this for sure.  But it seems like the only reasonable way to do it.  In such a case I can see how a single might be created / destroyed before/after main is called.  Still since usage of such a singleton would be confined to the interior of the library, things should be OK.  Of course if the same type is used (maybe only declared in a header) things could get more complex - maybe two singletons of the same type might exist???.   Because of all this - and because I'm paranoid in general, I created is_destroyed basically to support runtime asserts and to gate things which might happen and might be legal.  It's basically defensive programming.  Down side is that it's really hard to guarantee that is_destroyed has the value that I expect of it.  In a simple case where types aren't dynamically loaded - I feel OK about this.  In other cases - it's much harder to say.  Given that all this depends so much on other/unknowable things, I don't think anyone can say much more than this.   We're stuck with dependence on testing.

> Username: Flamefire  
> Created_at: 2018-11-08 18:27:35 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#discussion_r232009933  

> I would expect is_destroyed to always return false.  
  
Not when the singleton is destroyed. This happens **after** main ended during static destruction. For this I created the test #129:  
  
- In the context of a single executable (to avoid the shared library issue and hence be able to rely on the C++ standard) do the following:  
- Create a controller singleton: https://github.com/boostorg/serialization/pull/131/files#diff-2e5ac64fa01f589ee088b08db23a0bfaR34  
- Add a class `Foo` to be the singleton (e.g. by inheriting from singleton<>): https://github.com/boostorg/serialization/pull/131/files#diff-2e5ac64fa01f589ee088b08db23a0bfaR48  
- In the ctor of `Foo` refer to the `controller`: https://github.com/boostorg/serialization/pull/131/files#diff-2e5ac64fa01f589ee088b08db23a0bfaR51  
- This causes `controller` to be constructed before `Foo` and destructed after it (guaranteed by the C++ standard and you also have a test for this)  
- In the destructor of `Foo` tell the controller that we are destroyed: https://github.com/boostorg/serialization/pull/131/files#diff-2e5ac64fa01f589ee088b08db23a0bfaR57  
- In the destructor of `controller` check that `Foo` was indeed destroyed (or else there would be a memory leak as in boost 1.57): https://github.com/boostorg/serialization/pull/131/files#diff-2e5ac64fa01f589ee088b08db23a0bfaR65  
- Also check that `is_destroyed` now also returns false: https://github.com/boostorg/serialization/pull/131/files#diff-2e5ac64fa01f589ee088b08db23a0bfaR67  
  
As you can see there is no guessing. Everything is well defined and the `is_destroyed` function is tested and memory release ensured. As you can also see in #129 `is_destroyed` returns false in the dtor of `controller` which is a bug I fixed here (see all the comments in the code)  
  
>  Of course if the same type is used ...  maybe two singletons of the same type might exist???  
  
This is what I force in the crash test #128: The `tkmap` singleton is used in both shared libs. What happens is described above and can be easily verified by printf debugging (yes regular debugging works too but is cumbersome)


📁 src/extended_type_info.cpp

```diff
 126 |     if(NULL == get_key())
 127 |         return;
 128 |-     BOOST_ASSERT(! singleton<detail::ktmap>::is_destroyed());
```

> Username: jeking3  
> Created_at: 2018-11-07 13:07:47 UTC  
> Updated_at: 2018-11-07 13:09:24 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#discussion_r231496338  

The original author was suggesting that if the singleton is destroyed here, then something was quite wrong.  An invariant was broken somehow.  I agree with the original author on this one.  Why would the map have been destroyed AND something is calling unregister?  It sounds like there's a static uninitialization race somewhere.  Isn't this assertion still valid?  If not, there's still a race.

> Username: Flamefire  
> Created_at: 2018-11-07 13:19:46 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#discussion_r231499836  

Yes there is a broken invariant: With shared libs using static boost as described in the other occurence you commented on. Cause is simply implementation defined behaviour for shared libs (see difference Linux vs Windows and OSX)  
  
So no the assertion is not valid anymore hence its removal. This condition is instead caught by the runtime check 1 line below.  
  
Its is not a race as the destruction happens on unload in single-threaded way.

> Username: robertramey  
> Created_at: 2018-11-08 16:42:30 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#discussion_r231973044  

Hmmm - as the original author, I think my intention is pretty clear.  So far I haven't managed to trip this assertion.  If does occur then I'd like to know about it.  Now I'm aware that you've got some tests which which do trip this assertion.  When I've looked at your tests before I've not been convinced that they prove what you want to prove.  Having said that, I'm willing to look at more tests.  But I would like to see them more carefully constructed.  I'm aware that this requires a lot more effort but I don't see any other choice.

> Username: pdimov  
> Created_at: 2018-11-08 16:56:53 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#discussion_r231978933  

> So far I haven't managed to trip this assertion.  
  
That's because `is_destroyed` doesn't work.

> Username: robertramey  
> Created_at: 2018-11-08 17:45:06 UTC  
> Updated_at: 2018-11-08 17:46:09 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#discussion_r231996093  

Hmmm - how does it not work.  
'  
    static bool & get_is_destroyed(){  
        static bool is_destroyed;  
        return is_destroyed;  
    }  
public:  
    BOOST_DLLEXPORT static T & get_mutable_instance(){  
        BOOST_ASSERT(! get_singleton_module().is_locked());  
        return get_instance();  
    }  
    BOOST_DLLEXPORT static const T & get_const_instance(){  
        return get_instance();  
    }  
    BOOST_DLLEXPORT static bool is_destroyed(){  
        return get_is_destroyed();  
    }  
    BOOST_DLLEXPORT singleton(){  
        get_is_destroyed() = false;  
    }  
    BOOST_DLLEXPORT ~singleton() {  
        get_is_destroyed() = true;  
    }  
`  
  
where is the problem here?  I understand that the singleton destructor might not ever be called as it would happen post main time.  But that's not in anyone's control.  I don't know if one would say that that's an error.  In any case we don't depend on that and have no control over it anyway. The intention is to detect that case where some singleton is being uses after it's destructor has been called.  So to say it "doesn't work" would mean that ~singleton has been called but the is_destroyed still returns true.  I don't see any evidence of that happening.  Of course if assertions are being invoked - it would mean that singleton destructors are not being called in reverse sequence of the constructors.  The library is predicated on the assumption that does not happen.  Soooo, if this assertion is being invoked I would say that it's not an indication that the is_destroyed "doesn't work", it's more likely that the assumption of sequence of destruction/construction is not respected.  Of course this could be the scenario which might occur when one is invoking singletons from multiple dynamically loaded shared libraries.  Which I believe is the situtation we are trying to deal with in this case.  If this is indeed occurring - then the true question is - under what conditions does this occur and how can it be prevented.    
  
In other words, I believe that if the assertions are being invoked - that's it's a true violation of some assumption that I've made.  Not necessarily that the conditional is wrong.

> Username: pdimov  
> Created_at: 2018-11-08 17:53:29 UTC  
> Updated_at: 2018-11-08 17:53:30 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#discussion_r231998990  

The constructor and destructor of `singleton<detail::ktmap>` are never called, because a variable of such type is never created.

> Username: Flamefire  
> Created_at: 2018-11-08 18:39:37 UTC  
> Updated_at: 2018-11-08 18:39:38 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#discussion_r232013963  

> I understand that the singleton destructor might not ever be called as it would happen post main time.  
  
That's where you are mislead: The whole situation is about what happens "post main time": When all the singletons gets destructed and types get unregistered a mixup occurs and things go BOOM.  
  
I described the test I did in #129 here: https://github.com/boostorg/serialization/pull/131#discussion_r232009933. I argue that this is a very carefully constructed test as it completely relies on the C++ standard. It shows under what circumstances the problem (`is_destroyed` returns false for a destroyed singleton) occurs: When the class is not inherited from singleton<> as it is the case for `singleton<detail::ktmap>` as @pdimov  also noticed. (There are 2 tests: 1 where it is inherited and 1 where it is not, both are valid as per your docu and code) This is why the subclass `singleton_wrapper` is required: To listen to the destruction of `detail::ktmap`, not (only) `singleton<>`  
  
> If this is indeed occurring - then the true question is - under what conditions does this occur and how can it be prevented.  
  
The first part can be verified easily: Cherry-pick https://github.com/boostorg/serialization/pull/131/commits/7a34ef8b76e542560ac3895b5a3d68499f7ed422 and run the test #128 on linux in debug mode: The assertion **will** fire. I do not think this can be prevented because it is "implementation defined land" (shared libs), but it can be gracefully handled (by the runtime checks)  
  
Side note: What do you think is wrong with #128? I made this out of #111 (which is the minimum working example) with completely documented code. No tricks or anything. Or did I miss anything?

> Username: robertramey  
> Created_at: 2018-11-08 21:48:21 UTC  
> Updated_at: 2018-11-08 21:48:22 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#discussion_r232073182  

>The constructor and destructor of singleton<detail::ktmap> are never called, because a variable of such type is never created.  
Why do you think that?   
a) Certainly the get_instance which creates the singleton<detail::ktmap> is getting called.  Otherwise things would not work at all.  So the constructor must be getting called.  And there is a static instance created at pre-compile time so the constructor must be getting called there.  
b) I can't guarantee that the destructor is getting at post-main time as I don't have much access to it.  
c) Note that there is a test called "test_singleton" which verifies that singleton constructors are called and that destructors are called in reverse order.  See https://github.com/boostorg/serialization/blob/develop/test/test_singleton.cpp  
  
Now the question arises as to what happens when we've got multiple singletons of the same type. Such as in different shared libraries.  It might be possible that there would be some sort of mixup in that results in the wrong destructor being called.  But I don't see an easy way to track this down.

> Username: robertramey  
> Created_at: 2018-11-08 22:04:42 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#discussion_r232077949  

>That's where you are mislead: The whole situation is about what happens "post main time": When all the singletons gets destructed and types get unregistered a mixup occurs and things go BOOM.  
  
That's conceivable. I'll take another look at your test.  I'll look at "test_plain" as it seems to be simpler.  
  
>The first part can be verified easily: Cherry-pick 7a34ef8 and run the test #128 on linux in debug mode: The assertion will fire. I do not think this can be prevented because it is "implementation defined land" (shared libs), but it can be gracefully handled (by the runtime checks)  
  
Right - I don't have a linux system setup up for this.  But I'll still review the test.  But I want to review the best test so if you think there's a better one than #129/test_plain let me know.  
  
If your assessment of the problem above is correct, it should manifest itself replacing singleton<detail::ktmap> with some simpler type - Correct.  The reason I ask this is that I'd like to verify that it's an issue with singleton and something else in the serialization library.  Perhaps you'd like to make that change and report the results.  
  
If you assessment above is correct, then we need a workaround.  The workaround is not the complex in theory - just suppress the assertion at post main time. I'm sure a way to do that could be found.  But of course everything is trickier than it looks.  In particular, what is post-main time when a dll is unloaded?  Is it really post main time when things go south - or is it at dll unload time? etc. etc....

> Username: pdimov  
> Created_at: 2018-11-08 22:45:18 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#discussion_r232088304  

> Certainly the `get_instance` which creates the `singleton<detail::ktmap>` is getting called. Otherwise things would not work at all. So the constructor must be getting called.  
  
`singleton<detail::ktmap>::get_instance` is a static member. It creates an instance of `detail::ktmap` and returns it. This however has nothing to do with the constructor of `singleton<detail::ktmap>`.

> Username: pdimov  
> Created_at: 2018-11-08 22:48:02 UTC  
> Updated_at: 2018-11-08 22:48:27 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#discussion_r232088917  

In the case where `X` derives from `singleton<X>`, constructing a `X` necessarily constructs the base `singleton<X>`. `detail::ktmap`, however, is a typedef for `std::multiset<>`, which does not derive from `singleton<>`.  
  
This is all explained in Alexander's test which checks both cases (he calls them "plain" singleton and "inherited" singleton.)

> Username: robertramey  
> Created_at: 2018-11-08 22:56:11 UTC  
> Updated_at: 2018-11-08 22:56:12 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#discussion_r232090762  

> This however has nothing to do with the constructor of singleton<detail::ktmap>  
  
Hmmm - does this not invoke the constructor of singleton<detail::ktmap> at pre-main time?  
  
template<class T>  
T & singleton< T >::m_instance = singleton< T >::get_instance();

> Username: pdimov  
> Created_at: 2018-11-08 23:04:28 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#discussion_r232092891  

No; `T` here is `detail::ktmap`, so it defines a variable of type `detail::ktmap&`, initialized with the result of calling `singleton<detail::ktmap>::get_instance()`. Nothing here constructs a `singleton<detail::ktmap>`.

> Username: robertramey  
> Created_at: 2018-11-08 23:51:02 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#discussion_r232102415  

OK - I see this now and it's not what I intended.  I'll look into this

> Username: robertramey  
> Created_at: 2018-11-09 02:01:30 UTC  
> Updated_at: 2018-11-09 02:01:31 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#discussion_r232123202  

OK - I'm buying this.  I'll put in my current version and run some tests on my machine then check it in.  I did change the m_instance from a reference to a pointer to avoid an error with UBSAN set.  Hopefully that won't break anything.

> Username: Flamefire  
> Created_at: 2018-11-09 07:30:17 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#discussion_r232161582  

@pdimov already said the vital parts but I still want to answer the following to avoid future problems:  
  
> But I want to review the best test so if you think there's a better one than #129/test_plain let me know.  
  
Actually this is answered in the description of #129: There are 2 possible use cases for singleton. `extended_type_info` uses the inherited-paradigm and `tkmap` the plain-paradigm. For both `is_destroyed` has to work. That's why **both**  tests are required. So please do not only merge one of them or it will be broken again.


---

## Comment 3

> Username: Flamefire  
> Created_at: 2018-11-07 13:23:52 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#issuecomment-436620991  

>When are all uses of singleton in this library guaranteed to be constructed and destroyed?  
> If they initialize at static construction time then we're okay.  
  
They are: https://github.com/boostorg/serialization/pull/131/files#diff-3f1e861be15fd4926f5e5b00388f2309R168  
  
> This implementation could still suffer from a multi-threaded static initialization race.  
  
Not for static function-local vars which must be thread safe as mandated by the C++ standard.  
  
> I'm also concerned that the static destruction order is not understood. Replacing assertions with conditional logic confirms this.  
  
Only for the case of shared libraries where the destruction order is different. From observations I can conclude that on Linux same types are destructed together even when in different shared libraries. You can easily validate this by putting a printf in the destructor of `singleton_wrapper` with the type name and address. (This is how I found the root cause)

---

## Comment 4

> Username: jeking3  
> Created_at: 2018-11-07 13:24:44 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#issuecomment-436621223  

I have the expanded CI suite running on this PR:  
https://github.com/jeking3/serialization/pull/3

---

## Comment 5

> Username: jeking3  
> Created_at: 2018-11-07 13:29:23 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#issuecomment-436622538  

@Flamefire thanks for the answers.  I didn't pick that up from the previous conversations.

---

## Comment 6

> Username: Flamefire  
> Created_at: 2018-11-07 13:32:57 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#issuecomment-436623566  

Sure. I suggest also reading the description of https://github.com/boostorg/serialization/pull/105 and #104 for more details. But I think the important parts are now summarized here. Thanks for asking.

---

## Comment 7

> Username: jeking3  
> Created_at: 2018-11-07 19:00:56 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#issuecomment-436739073  

UBSAN identified some issues in the area - this one happens many times during the tests:  
```  
testing.capture-output ../../bin.v2/libs/serialization/test/test_diamond_xml_archive.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/test_diamond_xml_archive.run  
====== BEGIN OUTPUT ======  
../../boost/serialization/singleton.hpp:175:12: runtime error: reference binding to null pointer of type 'const struct map'  
    #0 0x7f5cc1f16b95 in boost::serialization::singleton<boost::archive::detail::extra_detail::map<boost::archive::binary_iarchive> >::get_instance() ../../boost/serialization/singleton.hpp:175  
    #1 0x7f5cc1f0ebd4 in __static_initialization_and_destruction_0 ../../boost/serialization/singleton.hpp:202  
    #2 0x7f5cc1f0ebf1 in _GLOBAL__sub_I_binary_iarchive.cpp ../../libs/serialization/src/binary_iarchive.cpp:40  
    #3 0x7f5cc20e42d9  (/lib64/ld-linux-x86-64.so.2+0x102d9)  
    #4 0x7f5cc20e43c2  (/lib64/ld-linux-x86-64.so.2+0x103c2)  
    #5 0x7f5cc20d5299  (/lib64/ld-linux-x86-64.so.2+0x1299)  
  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
  
    LD_LIBRARY_PATH="/home/travis/build/jeking3/boost-root/bin.v2/libs/filesystem/build/gcc-8/debug/cxxstd-03-iso/visibility-hidden:/home/travis/build/jeking3/boost-root/bin.v2/libs/serialization/build/gcc-8/debug/cxxstd-03-iso/visibility-hidden:/home/travis/build/jeking3/boost-root/bin.v2/libs/system/build/gcc-8/debug/cxxstd-03-iso/visibility-hidden:/usr/bin:/usr/lib:/usr/lib32:/usr/lib64:$LD_LIBRARY_PATH"  
export LD_LIBRARY_PATH  
  
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
     "../../bin.v2/libs/serialization/test/test_diamond_xml_archive.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/test_diamond_xml_archive"   > "../../bin.v2/libs/serialization/test/test_diamond_xml_archive.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/test_diamond_xml_archive.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../bin.v2/libs/serialization/test/test_diamond_xml_archive.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/test_diamond_xml_archive.output"  
    echo EXIT STATUS: $status >> "../../bin.v2/libs/serialization/test/test_diamond_xml_archive.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/test_diamond_xml_archive.output"  
    if test $status -eq 0 ; then  
        cp "../../bin.v2/libs/serialization/test/test_diamond_xml_archive.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/test_diamond_xml_archive.output" "../../bin.v2/libs/serialization/test/test_diamond_xml_archive.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/test_diamond_xml_archive.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../bin.v2/libs/serialization/test/test_diamond_xml_archive.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/test_diamond_xml_archive.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
...failed testing.capture-output ../../bin.v2/libs/serialization/test/test_diamond_xml_archive.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/test_diamond_xml_archive.run...  
```

---

## Comment 8

> Username: jeking3  
> Created_at: 2018-11-07 19:05:04 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#issuecomment-436740359  

The valgrind build looks like it has identified some other things, but I don't see anything related to singletons or static construction or destruction.    
  
https://api.travis-ci.org/v3/job/451983024/log.txt  
  
The windows builds don't look good either, but it may not be related to these changes.

---

## Comment 9

> Username: jeking3  
> Created_at: 2018-11-07 19:07:57 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#issuecomment-436741207  

On the windows side this is appearing in all of the builds so far, but it may not be related to the singleton changes.  
```  
msvc.link.dll bin.v2\libs\serialization\test\msvc-14.1\release\address-model-64\threading-multi\dll_polymorphic_derived2-vc141-mt-x64-1_69.dll  
   Creating library bin.v2\libs\serialization\test\msvc-14.1\release\address-model-64\threading-multi\dll_polymorphic_derived2-vc141-mt-x64-1_69.lib and object bin.v2\libs\serialization\test\msvc-14.1\release\address-model-64\threading-multi\dll_polymorphic_derived2-vc141-mt-x64-1_69.exp  
polymorphic_derived2.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) public: virtual __cdecl polymorphic_base::~polymorphic_base(void)" (__imp_??1polymorphic_base@@UEAA@XZ) referenced in function "public: virtual __cdecl polymorphic_derived2::~polymorphic_derived2(void)" (??1polymorphic_derived2@@UEAA@XZ)  
polymorphic_derived2.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) public: __cdecl polymorphic_base::polymorphic_base(void)" (__imp_??0polymorphic_base@@QEAA@XZ) referenced in function "public: static void __cdecl boost::serialization::access::construct<class polymorphic_derived2>(class polymorphic_derived2 *)" (??$construct@Vpolymorphic_derived2@@@access@serialization@boost@@SAXPEAVpolymorphic_derived2@@@Z)  
polymorphic_derived2.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) public: __cdecl polymorphic_base::polymorphic_base(class polymorphic_base const &)" (__imp_??0polymorphic_base@@QEAA@AEBV0@@Z) referenced in function "public: __cdecl polymorphic_derived2::polymorphic_derived2(class polymorphic_derived2 const &)" (??0polymorphic_derived2@@QEAA@AEBV0@@Z)  
polymorphic_derived2.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) public: class polymorphic_base & __cdecl polymorphic_base::operator=(class polymorphic_base const &)" (__imp_??4polymorphic_base@@QEAAAEAV0@AEBV0@@Z) referenced in function "public: class polymorphic_derived2 & __cdecl polymorphic_derived2::operator=(class polymorphic_derived2 const &)" (??4polymorphic_derived2@@QEAAAEAV0@AEBV0@@Z)  
bin.v2\libs\serialization\test\msvc-14.1\release\address-model-64\threading-multi\dll_polymorphic_derived2-vc141-mt-x64-1_69.dll : fatal error LNK1120: 4 unresolved externals  
        call "bin.v2\standalone\msvc\msvc-14.1\address-model-64\architecture-x86\msvc-setup.bat"  >nul  
 link /NOLOGO /INCREMENTAL:NO /DLL /MACHINE:X64 /MANIFEST /subsystem:console /out:"bin.v2\libs\serialization\test\msvc-14.1\release\address-model-64\threading-multi\dll_polymorphic_derived2-vc141-mt-x64-1_69.dll" /IMPLIB:"bin.v2\libs\serialization\test\msvc-14.1\release\address-model-64\threading-multi\dll_polymorphic_derived2-vc141-mt-x64-1_69.lib"    @"bin.v2\libs\serialization\test\msvc-14.1\release\address-model-64\threading-multi\dll_polymorphic_derived2-vc141-mt-x64-1_69.dll.rsp"  
        if %ERRORLEVEL% NEQ 0 EXIT %ERRORLEVEL%  
      
...failed msvc.link.dll bin.v2\libs\serialization\test\msvc-14.1\release\address-model-64\threading-multi\dll_polymorphic_derived2-vc141-mt-x64-1_69.dll bin.v2\libs\serialization\test\msvc-14.1\release\address-model-64\threading-multi\dll_polymorphic_derived2-vc141-mt-x64-1_69.lib...  
...removing bin.v2\libs\serialization\test\msvc-14.1\release\address-model-64\threading-multi\dll_polymorphic_derived2-vc141-mt-x64-1_69.lib  
```  
https://ci.appveyor.com/project/jeking3/serialization/build/job/344fh48thj6hs66f?fullLog=true#L3555

---

## Review 10 [Changes requested]

> Username: jeking3  
> Created_at: 2018-11-07 19:09:03 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/serialization/pull/131#pullrequestreview-172651525  

Need to resolve the UBSAN that's related:  
```  
../../boost/serialization/singleton.hpp:175:12: runtime error: reference binding to null pointer of type 'const struct map'  
    #0 0x7f5cc1f16b95 in boost::serialization::singleton<boost::archive::detail::extra_detail::map<boost::archive::binary_iarchive> >::get_instance() ../../boost/serialization/singleton.hpp:175  
    #1 0x7f5cc1f0ebd4 in __static_initialization_and_destruction_0 ../../boost/serialization/singleton.hpp:202  
    #2 0x7f5cc1f0ebf1 in _GLOBAL__sub_I_binary_iarchive.cpp ../../libs/serialization/src/binary_iarchive.cpp:40  
    #3 0x7f5cc20e42d9  (/lib64/ld-linux-x86-64.so.2+0x102d9)  
    #4 0x7f5cc20e43c2  (/lib64/ld-linux-x86-64.so.2+0x103c2)  
    #5 0x7f5cc20d5299  (/lib64/ld-linux-x86-64.so.2+0x1299)  
```  
https://api.travis-ci.org/v3/job/451983023/log.txt

---

## Comment 11

> Username: robertramey  
> Created_at: 2018-11-08 04:04:39 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#issuecomment-436866388  

Note regarding some UBSAN errors/warnings.  The serialization library in some cases depends on undefined behavior in order to function. In particular with the serialization of objects through pointers.  In particular, particular in a diamond inheritance situation.  Some of these may be "fixable" but it's possible that some may not and we'll just have to depend on testing to verify that the code actually works.  These will need to be considered individually.

---

## Comment 12

> Username: Flamefire  
> Created_at: 2018-11-08 08:05:33 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#issuecomment-436906844  

This particular UB is probably caused by the required instantiation force thingy @pdimov mentioned https://github.com/boostorg/serialization/pull/105#discussion_r225619041  
  
I argued that this should be done in a seaparate PR to not mix things up.

---

## Review 13 [Commented]

> Username: jeking3  
> Created_at: 2018-11-08 13:27:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/serialization/pull/131#pullrequestreview-172953287  

📁 include/boost/serialization/singleton.hpp

```diff
 166 |+         // use a wrapper so that types T with protected constructors can be used
 167 |+         // Using a static function member avoids LNK1179
 168 |+         static detail::singleton_wrapper< T > t;
```

> Username: jeking3  
> Created_at: 2018-11-08 13:27:03 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#discussion_r231885144  

This line initializes a class of type T.  This means returning `t` would return a `T&` on (new) line 177 below.  This means (new) lines 175, 160-162 are unnecessary.

> Username: Flamefire  
> Created_at: 2018-11-08 13:34:02 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#discussion_r231887360  

Those lines are required so the initialization-at-startup works. https://github.com/boostorg/serialization/pull/131/files#diff-3f1e861be15fd4926f5e5b00388f2309R160 and https://github.com/boostorg/serialization/pull/131/files#diff-3f1e861be15fd4926f5e5b00388f2309R202 should suffice but they need the `use` call or it will be optimized away.


---

## Review 14 [Commented]

> Username: jeking3  
> Created_at: 2018-11-08 13:29:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/serialization/pull/131#pullrequestreview-172954087  

📁 include/boost/serialization/singleton.hpp

```diff
 200 |+ // at startup time as described in http://tinyurl.com/ljdp8
 201 | template<class T>
 202 | T & singleton< T >::m_instance = singleton< T >::get_instance();
```

> Username: jeking3  
> Created_at: 2018-11-08 13:29:02 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#discussion_r231885804  

Why do this at all?  On the first call to get_instance() for a type, it would instantiate the class when needed.

> Username: Flamefire  
> Created_at: 2018-11-08 13:31:09 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#discussion_r231886498  

Because it is needed to be instantiated at startup time (requirement of Boost.Serialization)  
  
This is checked by the `locked` feature https://github.com/boostorg/serialization/pull/131/files#diff-3f1e861be15fd4926f5e5b00388f2309R188

> Username: jeking3  
> Created_at: 2018-11-08 13:47:26 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#discussion_r231892656  

I see, the requirements are to avoid locking so this needs to be done at initialization time.  This is a pretty funky way to do it. :)

> Username: Flamefire  
> Created_at: 2018-11-08 13:58:58 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#discussion_r231896447  

That's why I added the comment and a reference so future readers know why this is done. I deem this an important part of this PR

> Username: robertramey  
> Created_at: 2018-11-08 16:49:11 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#discussion_r231975693  

Note on locking.  Strictly speaking this "locking" feature is not necessary.  In the serialization library I wanted all my singletons to be constant - ie not mutable.  I wanted to trap attempts to modify them after main is called.  But of course that means I want to force creation on program/shared library load.  So I'm fine with the comment.  I would recommend that the comment not point to a link - even if the text is longer.  The serialization library will likely persist in usage long after the link is gone.

> Username: Flamefire  
> Created_at: 2018-11-08 18:44:57 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#discussion_r232015848  

I actually think it is required as a test that construction happens before main. If it would be constructed on first use, you won't be able to deserialize a derived class from a pointer for which you didn't "use" the singleton yet (e.g. by serializing it or refering to it otherwise)  
  
I can remove the link if you wish or replace it by the original: https://groups.google.com/forum/#!topic/microsoft.public.vc.language/kDVNLnIsfZk (in the hope that even in the unlikely case google will vanish services as archive.org can be used to retrieve it), but the essentials are summarized in the 1 sentence already. Your call.


---

## Comment 15

> Username: robertramey  
> Created_at: 2018-11-09 02:13:46 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#issuecomment-437225724  

msvc.link.dll bin.v2\libs\serialization\test\msvc-14.1\release\address-model-64\threading-multi\dll_polymorphic_derived2-vc141-mt-x64-1_69.dll  
   Creating library bin.v2\libs\serialization\test\msvc-14.1\release\address-model-64\threading-multi\dll_polymorphic_derived2-vc141-mt-x64-1_69.lib and object bin.v2\libs\serialization\test\msvc-14.1\release\address-model-64\threading-multi\dll_polymorphic_derived2-vc141-mt-x64-1_69.exp  
polymorphic_derived2.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) public: virtual __cdecl polymorphic_base::~polymorphic_base(void)" (__imp_??1polymorphic_base@@UEAA@XZ) referenced in function "public: virtual __cdecl polymorphic_derived2::~polymorphic_derived2(void)" (??1polymorphic_derived2@@UEAA@XZ)  
...  
  
This is not related to the "singleton" problem.  In working to find the error in test_dll_exported on some platforms (which I thought was related to the "singleton" problem), I discovered an instance where mixing exported types serialized via base class pointers to polymorphic archives (LOL - now that is a mouthful) wouldn't link.  This turns out to be trickier than I thought as I had to navigate the "visibility/import/export" labyrinth.  I've got it fixed on platforms other than windows which I cannot test here.  So this is still pending.   
  
As an aside - the test_dll_exported test failure on some very few platforms is also related to "visibility"  This could be verified by building with visibility=global.  This whole episode wasn't made any easier by the fact that global build with b2 changed the default visibility rules while I was doing this and for a while maintained different settings between develop and master.  Very, very, very confusing and hard to deal with.

---

## Comment 16

> Username: jeking3  
> Created_at: 2018-11-09 02:45:36 UTC  
> Updated_at: 2018-11-09 02:50:54 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#issuecomment-437231414  

I have fixes pending for the polymorphic.  They are building right now in https://github.com/jeking3/serialization/pull/4  
  
They are mixed in with better CI job definitions, and I have enabled the compilation of all the examples as well.  
  
Note UBSAN and valgrind jobs are still showing errors... but these jobs do not have the singleton fixes in them yet.

---

## Comment 17

> Username: robertramey  
> Created_at: 2018-11-09 03:04:04 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#issuecomment-437234613  

I'm running the singleton fixes now my machine.  Probably take a number of hours.  I still have pending questions regarding the -fPIC switch with b2 which I would like to see considered in a more general way. I expect UBSAN to show errors regardless of what I do.  This should eliminate one source of these errors but I'm pretty sure they can never be totally eliminated.

---

## Comment 18

> Username: pdimov  
> Created_at: 2018-11-09 03:14:10 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#issuecomment-437236135  

> I still have pending questions regarding the -fPIC switch with b2 which I would like to see considered in a more general way.  
  
`-fPIC` should be a propagated feature, but that needs to be implemented on Boost.Build's side. I mentioned that on the list - https://lists.boost.org/Archives/boost/2018/11/244239.php - but the more appropriate place would probably be an issue in Build.

---

## Comment 19

> Username: Flamefire  
> Created_at: 2018-11-09 08:04:27 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#issuecomment-437281037  

@robertramey What do you think of adding `-fPIC` to the bjam file manually: https://github.com/jeking3/serialization/pull/3/commits/3bf664304b14ba1ca55d15d79ae4d4fdae9e2b13#diff-42dd6ec1330a7c47aaccf2ab2b8f1e02

---

## Comment 20

> Username: robertramey  
> Created_at: 2018-11-09 08:33:18 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#issuecomment-437287455  

>What do you think of adding -fPIC to the bjam file manually  
  
I'm pretty confused about the PIC thing.  I understand what it's for.  But in order to be changing only one thing at a time - I didn't include that change.  I think I remember reading that without PIC the addresses are fixed up when the shared library is loaded in to the main programs address space.  I don't know if that's actually true.  I'm sure Peter will have something to say about this.  In any case, it seems that all the tests that might be related to singleton are passing.  If it's not actually necessary, I'd prefer to not include it as it injects another confusing variable into the mix.  The only failure is test_dll_exported which is failing only under windows.  I'm quite sure this is related to visibility.  
  
Right now, I'm trying to get the last bug out so I can merge to master so it can come out in the soon to be released version.  I'm already overdue and that's a problem.  
  
I've take a cursory look at your tests and they look OK.  Still I want to take a little more time and before I add them to the official test suite.  So they wouldn't go in until after we get this merge done.

---

## Comment 21

> Username: Flamefire  
> Created_at: 2018-11-09 08:46:03 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#issuecomment-437290577  

> But in order to be changing only one thing at a time - I didn't include that change  
  
Completely agreed. That's why I made separate PRs and even commits for that.  
  
>  I think I remember reading that without PIC the addresses are fixed up when the shared library is loaded in to the main programs address space.  
  
"PIC" = "Position Independent Code". A shared library may be loaded anywhere in the address space and hence the generated assembly must be aware of this and include relative references, fixups and the like. From a more practical point of view you can simply say: All object files linked into a shared library must be build with `-fPIC`, which includes static libs. You get a linker error telling you this.  
  
>  If it's not actually necessary, I'd prefer to not include it as it injects another confusing variable into the mix.   
  
I do think the test of #128 is valuable enough to avoid regressions. The bjam modification is generic enough to be included  
  
> I've take a cursory look at your tests and they look OK. Still I want to take a little more time and before I add them to the official test suite. So they wouldn't go in until after we get this merge done.  
  
With the time pressure and the critical issue fixed by this I do agree the merge to master is most important. Just don't forget those tests (especially the "easier" #129) afterwards to avoid this in the future. I chose this kinda TDD approach so this issue **stays** fixed.

---

## Comment 22

> Username: jeking3  
> Created_at: 2018-11-09 12:14:32 UTC  
> Updated_at: 2018-11-09 12:22:30 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#issuecomment-437342567  

On the polymorphic changes, I moved my comments to #137.

---

## Comment 23

> Username: Flamefire  
> Created_at: 2018-11-09 12:17:02 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#issuecomment-437343138  

Nice, but as this is an unrelated issue can you open another PR and discuss it there?

---

## Comment 24

> Username: jeking3  
> Created_at: 2018-11-09 12:18:49 UTC  
> Updated_at: 2018-11-09 12:19:47 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#issuecomment-437343495  

Let's discuss the polymorphic on #137.  Good suggestion.

---

## Comment 25

> Username: robertramey  
> Created_at: 2018-11-17 19:31:34 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#issuecomment-439641739  

I believe this is resolved.  If anyone is unhappy about this, please open a new PR.

---

## Comment 26

> Username: PhilippMuenzel  
> Created_at: 2018-12-04 19:49:55 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#issuecomment-444233800  

How do I find out which PR has been merged to the official boost release version? In other words, is this included in the boost 1.69 release or will it be?

---

## Comment 27

> Username: jeking3  
> Created_at: 2018-12-04 20:11:44 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#issuecomment-444240869  

It appears all commits in develop are in master, so changes related to this (these exact fixes were not necessarily applied, as the PR was not merged) were likely committed.

---

## Comment 28

> Username: PhilippMuenzel  
> Created_at: 2018-12-04 21:24:19 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#issuecomment-444263735  

do you have a commit hash that contains the fix that I could search for to find out which release version is fixed?

---

## Comment 29

> Username: jeking3  
> Created_at: 2018-12-04 21:27:51 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#issuecomment-444264878  

These fixes will be in 1.69.0, which is not yet released.  If you pull the latest develop or master, barring any critical issues, that's what will be in 1.69.0.

---

## Comment 30

> Username: PhilippMuenzel  
> Created_at: 2018-12-04 21:43:11 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#issuecomment-444270026  

thank you 👍

---

## Comment 31

> Username: Flamefire  
> Created_at: 2018-12-05 08:23:30 UTC  
> Url: https://github.com/boostorg/serialization/pull/131#issuecomment-444399041  

@PhilippMuenzel https://github.com/boostorg/serialization/commit/f297d80d6ef55ac66525320d0477c17806aa57cd contains my fix (among other unrelated changes)

---
