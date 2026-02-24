# #333 Make visibility feature not propagated [Merged]

> Username: Lastique  
> Created at: 2018-08-20 12:20:48 UTC  
> Updated at: 2021-10-02 21:19:39 UTC  
> Merged at: 2018-08-24 21:24:44 UTC  
> Closed at: 2018-08-24 21:24:44 UTC  
> Url: https://github.com/boostorg/build/pull/333  

This allows to enable hidden visibility on a library without affecting its dependencies, which may not (yet) support hidden visibility by default.

---

## Comment 1

> Username: Lastique  
> Created_at: 2018-08-24 21:23:26 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-415886182  

Could someone take a look, please? I would like to use this feature in Boost.Log.

---

## Comment 2

> Username: grafikrobot  
> Created_at: 2018-08-24 21:25:15 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-415886575  

Sorry about the delay.. Forgot I was waiting on the CI builds.

---

## Comment 3

> Username: Lastique  
> Created_at: 2018-08-24 21:30:33 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-415887707  

Thanks!

---

## Comment 4

> Username: pdimov  
> Created_at: 2018-09-30 05:35:46 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-425696114  

Consider reverting this. Now that the default is hidden, this makes it hard (to impossible) to use the UB sanitizer, which fails with `runtime error: member call on address ... which does not point to an object of type ...` all over the place. Propagation is natural for such features, and in this case for `b2 visibility=global` to propagate to dependencies is exactly what is needed.

---

## Comment 5

> Username: pdimov  
> Created_at: 2018-09-30 06:06:37 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-425697310  

Also, if you have a dependency chain of the form -> lib1/shared -> lib2/static -> lib3/static, it makes no sense for the visibility to not propagate from lib1 to lib2/lib3.

---

## Comment 6

> Username: Lastique  
> Created_at: 2018-09-30 09:23:21 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-425707112  

> this makes it hard (to impossible) to use the UB sanitizer, which fails with runtime error: member call on address ... which does not point to an object of type ... all over the place.  
  
I would say then we need to make Boost.Build aware of sanitizers and make a workaround just for the case when the sanitizers are used.  
  
> Propagation is natural for such features  
  
No, and this was why I made this PR. There was a moment when not all libraries supported hidden visibility (Boost.Regex) and I wanted to use this feature on one of the dependent libraries (Boost.Log). So the support for visibility is a property of a given library, not something users or upper-level libraries can blindly turn on or off for their dependencies.  
  
Granted, the visibility issues were (I think) fixed since then, so it should work even if reverted. I just don't think this is the right thing to do.  
  
> Also, if you have a dependency chain of the form -> lib1/shared -> lib2/static -> lib3/static, it makes no sense for the visibility to not propagate from lib1 to lib2/lib3.  
  
I disagree. Visibility of symbols defined by lib2 and lib3 is lib2's and lib3's business. If they require everything to be globally visible then there may be a reason to do so (like maybe they rely on comparing pointers to global stuff) and lib1 has to oblige. Now, you may want to complain to lib2 and lib3 authors if you think things can be improved.

---

## Comment 7

> Username: apolukhin  
> Created_at: 2018-09-30 10:51:17 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-425711926  

I see two use cases here:  
* setting visibility to global for a single target (for making a test with global visibility, or because that particular target fails with hidden visibility). For that use case the visibility should not be propagated.  
* setting visibility recursively for a chain of targets (to workaround issue in standard library, sanitizer or in a deeply nested library). This requires a propagation.  
  
I do not like the second  use case at all. Issues should be fixed in appropriate places. The more it hurts to workaround the issue, the more effort will be put by the author to fix it rather than workaround.

---

## Comment 8

> Username: pdimov  
> Created_at: 2018-09-30 12:40:55 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-425718134  

I really don't see how one can "disagree". If you have a dependency chain like I outlined, it's completely ridiculous for the visibility to not propagate from the shared library to its static dependencies. You end up with a shared library with half of its symbols hidden and no control over them.

---

## Comment 9

> Username: pdimov  
> Created_at: 2018-09-30 12:56:21 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-425719159  

Tell you what, if you're so insistent on non-propagation, let's add another feature that _does_ propagate, so that you can keep using your broken one, and others can use the correct one. That way everyone would be happy.

---

## Comment 10

> Username: pdimov  
> Created_at: 2018-09-30 13:24:10 UTC  
> Updated_at: 2018-09-30 13:25:55 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-425720789  

> Visibility of symbols defined by lib2 and lib3 is lib2's and lib3's business.  
  
That's not true because the visibility of lib3's symbols (as determined by the `visibility` feature, which we're discussing here) is determined by lib2, not lib3. If it links to `lib3/<visibility>global`, lib3's symbols become global, with lib1 having no control over them. And if it links to just `lib3`, lib3's symbols become hidden, with lib1 again having no control over them.  
  
And if you have lib3 <- lib1 -> lib2 -> lib3, you now are forced in lib1 to match whatever lib2 does with lib3, or you'll have conflicting properties.  
  
No amount of complaining to the authors of lib2 and lib3 will fix it, because it's not their fault. You have to edit lib2's Jamfile for each build. (`visibility=global,shared` to build two variants of lib1 at once is of course out of the question.)  
  
If the visibility of lib3's symbols was supposed to be strictly lib3's business, there would be no need for a feature. Features are by definition what users of lib3 specify.

---

## Comment 11

> Username: Lastique  
> Created_at: 2018-09-30 13:51:46 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-425722367  

> That's not true because the visibility of lib3's symbols (as determined by the visibility feature, which we're discussing here) is determined by lib2, not lib3.  
  
Not exactly. It is determined by lib3, if it wants to (i.e. if it has `<visibility>` in its requirements). If it doesn't have a preference then `<visibility>hidden` is the default, unless lib2 overrides this as you described. In practice, I don't think any library in Boost does such an override yet, and I don't see why anyone would want to do this (I mean, permanently, not just to temporarily work around a bug in the sanitizer). Frankly, I didn't even consider that such an override is possible.  
  
> And if you have lib3 <- lib1 -> lib2 -> lib3, you now are forced in lib1 to match whatever lib2 does with lib3, or you'll have conflicting properties.  
  
No, the intention is that lib3 always has visibility that written in its Jamfile (or the default, which is hidden; no pun intended). You just don't do the visibility override for the dependency and don't match anything. If there is a way to enforce this in Boost.Build (i.e. prohibit overrides) then I'd rather do that. I'm not aware of a way to do this.  
  
> If the visibility of lib3's symbols was supposed to be strictly lib3's business, there would be no need for a feature. Features are by definition what users of lib3 specify.  
  
If so then I don't understand how one should use Boost.Build to implement the feature, aside from hardcoding different compiler switches in every library's Jamfile. Remember, this feature appeared to (a) provide a portable way to control visibility in libraries and (b) make hidden visibility the default. It was never meant as a user configurable option.  
  
> Tell you what, if you're so insistent on non-propagation, let's add another feature that does propagate, so that you can keep using your broken one, and others can use the correct one. That way everyone would be happy.  
  
Keeping the spite aside, this may actually be a viable option, except that the "propagating" feature would force the global visibility everywhere despite the library preference (i.e. if `<force-global-visibility>on` then assume `<visibility>global`, even if the project specifies otherwise). Naturally, users would be responsible for the possible fallout.

---

## Comment 12

> Username: pdimov  
> Created_at: 2018-09-30 14:03:24 UTC  
> Updated_at: 2018-09-30 14:03:47 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-425723081  

We're obviously not understanding each other. Features are settable by the user of the library. It makes no sense to have a feature that the user is not supposed to set.  
  
If lib3 does nothing visibility-related, then when the user builds it with visibility=global he should get a library built with global visibility, and when he builds it with visibility=hidden he should get a library built with hidden visibility. That's how all features work.  
  
And the reason features propagate, as a rule, is to ensure consistency across the graph. When you depend on both lib1 and lib2, and lib1 depends on lib2 itself, and you want to build lib1 and lib2 with somefeature=foo, propagation ensures that the lib2 dependency of lib1 gets that same foo, otherwise you have a conflict.  
  
This is basic Boost.Build stuff so I don't see where we split ways.  
  
If the goal is for the user to be able to control independently the visibility of lib1 and lib2's symbols, then that's not possible because he has no way of overriding lib2's visibility.  
  
> Remember, this feature appeared to (a) provide a portable way to control visibility in libraries and (b) make hidden visibility the default. It was never meant as a user configurable option.  
  
That's just not what features are.  
  
But let's step back for a minute. What specific problem does propagation cause for you? What's the scenario you're trying to avoid?  
  
> if `<force-global-visibility>on`  
  
I don't want a "force global visibility" feature. I want a visibility feature that propagates. If I give it global, it propagates global. If I give it hidden, it propagates hidden. If I give it protected, it propagates protected. As it should.

---

## Comment 13

> Username: Lastique  
> Created_at: 2018-09-30 14:16:33 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-425723847  

> That's just not what features are.  
  
Well, then I guess I hijacked the features mechanism to achieve what we wanted. I just don't know of any other mechanism in Boost.Build that allows us to do this. If there is a better way to do this then by all means, let's revert this PR and https://github.com/boostorg/build/pull/331 and do it "the right way". I just don't want it reverted without a definitive direction for further improvement.  
  
Also, if you revert this support then please let everyone know. For example, I would need to revert changes in my libraries and restore the previous code for forcing hidden visibility with hardcoded compiler options.  
  
> What specific problem does propagation cause for you? What's the scenario you're trying to avoid?  
  
As I've already described, if lib2 doesn't support hidden visibility then any lib1 that depends on it cannot be built with hidden visibility (of lib1's symbols, at least), which is nonsense. This would also mean no default hidden visibility for Boost.

---

## Comment 14

> Username: pdimov  
> Created_at: 2018-09-30 14:18:20 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-425723968  

If lib2 doesn't support hidden visibility it puts in its requirements `<visibility>global`.

---

## Comment 15

> Username: pdimov  
> Created_at: 2018-09-30 14:18:59 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-425724010  

> This would also mean no default hidden visibility for Boost.  
  
Propagation does not affect the default.

---

## Comment 16

> Username: pdimov  
> Created_at: 2018-09-30 14:21:01 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-425724153  

Or, if lib2 is visibility-unaware, everyone has to link to it with `/<visibility>global`. Exactly the same as today. Propagation does not change anything.

---

## Comment 17

> Username: Lastique  
> Created_at: 2018-09-30 14:21:18 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-425724164  

> If lib2 doesn't support hidden visibility it puts in its requirements <visibility>global.  
  
IIUC, that means if lib1 has `<visibility>hidden` in its requirements and also depends on lib2 then the build will fail.  
  
> Propagation does not affect the default.  
  
It does, in the sense that you can't make it default to `<visibility>hidden` if one of the libraries cannot be built with it. Can you?

---

## Comment 18

> Username: Lastique  
> Created_at: 2018-09-30 14:23:20 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-425724279  

> IIUC, that means if lib1 has <visibility>hidden in its requirements and also depends on lib2 then the build will fail.  
  
Also, if lib1 doesn't have `<visibility>` in its requirements and the user builds it also without `visibility` option then b2 will pick `visibility=global` for everyone.

---

## Comment 19

> Username: pdimov  
> Created_at: 2018-09-30 14:23:36 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-425724301  

Propagation does not affect the default. If the default is hidden, it will be hidden either way.  
  
> IIUC, that means if lib1 has <visibility>hidden in its requirements and also depends on lib2 then the build will fail.  
  
I don't think it will. Let me check.

---

## Comment 20

> Username: Lastique  
> Created_at: 2018-09-30 14:27:21 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-425724536  

Also, if lib1 has no `<visibility>` in requirements, lib2 has `<visibility>global` and lib3 has no `<visibility>` then I'm not sure what `<visibility>` will be used for lib3. I would guess `<visibility>global`, as inferred from lib2. Which is not desired.

---

## Comment 21

> Username: pdimov  
> Created_at: 2018-09-30 14:33:55 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-425724973  

Works.  
  
```  
project root ;  
  
lib lib2 : lib2.cpp : <visibility>global ;  
lib lib1 : lib1.cpp lib2 : <visibility>hidden ;  
exe root : main.cpp lib1 lib2 : <visibility>protected ;  
```

---

## Comment 22

> Username: pdimov  
> Created_at: 2018-09-30 14:38:48 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-425725335  

Yes, it will be global, as inherited. The author of lib2 can override it to `lib3/<visibility>hidden`. Not every combination is achievable, but that's true in both cases, and the question is what's better.  
  
If you have a library lib2 depending on lib3, and neither of those does anything visibility-related, and you want to set lib3's visibility, the only way to do it is with propagation. Yes, this will set lib2's visibility as well. There's no other way.

---

## Comment 23

> Username: pdimov  
> Created_at: 2018-09-30 14:42:34 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-425725643  

> Also, if lib1 doesn't have <visibility> in its requirements and the user builds it also without visibility option then b2 will pick visibility=global for everyone.  
  
Since visibility is (at the moment) an optional feature, this (at present) results in it not having a value at all.

---

## Comment 24

> Username: Lastique  
> Created_at: 2018-09-30 14:50:13 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-425726228  

> Yes, it will be global, as inherited.  
  
Yes, I've just tested it. And this is a problem, as we'd have to patch every library to add `<visibility>hidden` and any future libraries will have to do this. One of the goals of making `<visibility>hidden` the default was that we don't have to do this.  
  
> The author of lib2 can override it to lib3/<visibility>hidden.  
  
If he overrides then it ruins user-configurability, doesn't it? Just forget about overriding. Can we prohibit it?  
  
> Since visibility is (at the moment) an optional feature, this (at present) results in it not having a value at all.  
  
An optional feature has a default value. "Optional" means it is not present in the build specification, but the feature is still acted upon (i.e. the relevant compiler flags are added).  
  
https://boostorg.github.io/build/manual/develop/index.html#bbv2.reference.features

---

## Comment 25

> Username: pdimov  
> Created_at: 2018-09-30 14:56:01 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-425726626  

When I say what happens, I mean what happens, as empirically tested. :-)  
  
> If he overrides then it ruins user-configurability, doesn't it?  
  
As I said, it's not possible to user-configure an indirect dependency _unless_ the direct dependency doesn't override the feature _and_ the feature propagates.  
  
If either of these doesn't hold, you can't. So if the feature doesn't propagate, you can't. So there's no regression. You couldn't before, and you still can't.

---

## Comment 26

> Username: Lastique  
> Created_at: 2018-09-30 15:04:59 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-425727337  

Yes, what I mean is that overriding ruins everyone's day - in your case it prevents configurability, in my case it messes up the default visibility. It's a solution to noone, so noone should ever use it.  
  
So this leaves us with having to explicitly specify `<visibility>hidden` everywhere, which also prohibits configurability, propagated feature or not. At least with the current implementation the default value is working.

---

## Comment 27

> Username: pdimov  
> Created_at: 2018-09-30 15:27:35 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-425728876  

I have no idea what you're talking about here. What is the specific scenario in which things don't work with propagation, but work without it?  
  
What I see at the moment is that my day is ruined without propagation, and not ruined with it. What's the counterexample? Paste it here so I can test it with/without.

---

## Comment 28

> Username: Lastique  
> Created_at: 2018-09-30 15:33:48 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-425729283  

```  
project root ;  
  
lib lib3 : lib3.cpp ;  
lib lib2 : lib2.cpp lib3 : <visibility>global ;  
lib lib1 : lib1.cpp lib2 ;  
exe root : main.cpp lib1 lib2 ;  
```  
  
This builds and uses lib3 with `<visibility>hidden` in the current implementation (non-propagated) and `<visibility>global` with propagated feature.

---

## Comment 29

> Username: pdimov  
> Created_at: 2018-09-30 15:35:52 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-425729401  

And changing it to  
  
```  
lib lib2 : lib2.cpp lib3/<visibility>hidden : <visibility>global ;  
```  
  
is a problem because?

---

## Comment 30

> Username: Lastique  
> Created_at: 2018-09-30 15:43:08 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-425729881  

Because:  
  
1. It breaks configurability, which you want to achieve and which is the original motivation for making the feature propagated.  
2. It hardcodes the assumption that lib3 supports hidden visibility in lib2, which is conceptually incorrect.  
3. It requires more changes to lib2 than necessary. I.e. instead of indicating just that it requires `<visibility>global` you also have to mark every dependency it has with `<visibility>hidden` or whatever that dependency supports.

---

## Comment 31

> Username: Lastique  
> Created_at: 2018-09-30 15:52:00 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-425730497  

If only Boost.Build supported a way to specify the default value for a (non-propagared) feature. I.e. for the case in https://github.com/boostorg/build/pull/333#issuecomment-425729283 `b2 visibility=global` would work the way we both want:  
  
- lib1 does not have a `<visibility>` property in the requirements, so it picks `<visibility>global` from the command line.  
- lib2 requires `<visibility>global`, so it is used.  
- lib3 does *not* inherit `<visibility>global` from lib2 and instead uses the default similar to lib1, which yields `<visibility>global` from the command line.

---

## Comment 32

> Username: pdimov  
> Created_at: 2018-09-30 15:52:47 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-425730576  

As I said, without propagation there's no configurability, so nothing is lost.  
  
The correct way to achieve global visibility for only lib2's symbols (and potentially only some of them), while letting dependencies be, is  
  
```  
lib lib3 : lib3.cpp ;  
lib lib2_impl : lib2.cpp ;  
lib lib2 : lib2_impl/<link>static/<visibility>global lib3 ;  
lib lib1 : lib1.cpp lib2 ;  
exe root : main.cpp lib1 lib2 ;  
```  
  
Now whatever "comes from above" flows down to lib3 unimpeded, while lib2 still enforces global visibility on the symbols it wants.

---

## Comment 33

> Username: pdimov  
> Created_at: 2018-09-30 15:57:02 UTC  
> Updated_at: 2018-09-30 15:57:53 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-425730896  

We already have a way to achieve what we both want - make `visibility` propagated and introduce `local-visibility` which isn't. lib2 would now be able to use `<local-visibility>global`.  
  
(The default-build will remain `<visibility>hidden`.)

---

## Comment 34

> Username: Lastique  
> Created_at: 2018-09-30 16:09:23 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-425731698  

> [The lib2_impl example]  
  
This might be difficult to implement if lib2 is built as a shared library and lib2_impl needs to export symbols. But yes, this is one way to do this.  
  
> We already have a way to achieve what we both want - make visibility propagated and introduce local-visibility which isn't. lib2 would now be able to use <local-visibility>global.  
  
Yes, its the two features option you mentioned before. Having the support for changing just the default of a single feature would make it much easier.  
  
Anyway, I agree the two-feature approach is the most viable solution, although I'm not sure how to implement it in Boost.Build efficiently. I'm not sure how one would derive `local-visibility` from `visibility` if the former is not enforced by the library.

---

## Comment 35

> Username: pdimov  
> Created_at: 2018-09-30 16:17:50 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-425732275  

`local-visibility` would need to have a (real) default (named `default` for instance) and then it's  
  
```  
toolset.flags gcc.compile OPTIONS <local-visibility>hidden : -fvisibility=hidden ;   
toolset.flags gcc.compile OPTIONS <local-visibility>protected : -fvisibility=protected ;   
toolset.flags gcc.compile OPTIONS <local-visibility>global : -fvisibility=default ;   
  
toolset.flags gcc.compile OPTIONS <local-visibility>default/<visibility>hidden : -fvisibility=hidden ;   
...  
```

---

## Comment 36

> Username: swatanabe  
> Created_at: 2018-09-30 16:50:09 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-425734433  

AMDG  
  
On 09/30/2018 10:17 AM, Peter Dimov wrote:  
> `local-visibility` would need to have a (real) default (named `default` for instance) and then it's  
>   
  
You don't need to make the two features independent.  
  
Just define the options using <local-visibility>, and  
make <visibility> a composite feature that uses <local-visibility>.  
  
```  
feature local-visibility : hidden protected global : optional ;  
feature visibility : hidden protected global :  
  optional composite propagated ;  
  
feature.compose <visibility>hidden : <local-visibility>hidden ;  
# Same for protected and global.  
```  
  
An explicit value of <local-visibility> will prevent <visibility>  
from being expanded, but will not prevent it from propagating  
to dependent targets.  If <local-visibility> is set in the  
default-build instead, then it will be overridden by <visibility>.  
I think this gives the desired behavior in all circumstances.  
  
Note: arguably, non-propagated features specified on the command  
line should behave like this automatically.  We already do it for  
free features like cxxflags.  
  
> ```  
> toolset.flags gcc.compile OPTIONS <local-visibility>hidden : -fvisibility=hidden ;   
> toolset.flags gcc.compile OPTIONS <local-visibility>protected : -fvisibility=protected ;   
> toolset.flags gcc.compile OPTIONS <local-visibility>global : -fvisibility=default ;   
>   
> toolset.flags gcc.compile OPTIONS <local-visibility>default/<visibility>hidden : -fvisibility=hidden ;   
> ...  
> ```  
>   
  
In Christ,  
Steven Watanabe

---

## Comment 37

> Username: Lastique  
> Created_at: 2018-09-30 17:52:16 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-425738565  

Thanks Steven and Peter. I've created https://github.com/boostorg/build/pull/345 and https://github.com/boostorg/boost/pull/198, which should be merged in that order.

---

## Comment 38

> Username: pdimov  
> Created_at: 2018-09-30 18:07:47 UTC  
> Updated_at: 2018-09-30 18:08:37 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-425739594  

Thanks Andrey.  
  
I'm not entirely sure about the superproject change. The choice affects Boost libraries linking to non-Boost ones such as f.ex. Zlib built from source. With default `<local-visibility>hidden`, Zlib will be built with global visibility, with default `<visibility>hidden`, Zlib will be built with hidden as well.  
  
Not sure which one we want here.

---

## Comment 39

> Username: Lastique  
> Created_at: 2018-09-30 19:12:51 UTC  
> Updated_at: 2018-09-30 19:29:33 UTC  
> Url: https://github.com/boostorg/build/pull/333#issuecomment-425744022  

I think having the same default visibility across all targets makes more sense. I will change https://github.com/boostorg/build/pull/345 and cancel https://github.com/boostorg/boost/pull/198.

---
