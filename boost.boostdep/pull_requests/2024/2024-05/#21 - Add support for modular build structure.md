# #21 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-05-01 15:41:48 UTC  
> Updated at: 2025-04-14 14:08:06 UTC  
> Merged at: 2025-04-14 14:08:06 UTC  
> Closed at: 2025-04-14 14:08:06 UTC  
> Url: https://github.com/boostorg/boostdep/pull/21  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-05-01 16:12:13 UTC  
> Url: https://github.com/boostorg/boostdep/pull/21#issuecomment-2088697225  

You'll have to walk me through these changes and explain why each is needed.  
  
* I don't think I ever plan to have a `modular` branch so I don't need the CI changes.  
* I don't like `explicit [ this ] [ that ] ;` and prefer the old school `this ; explicit this ; that ; explicit that ;`  
* The repetition of the DIST_DIR logic in every repo doesn't strike me as elegant. We should try to think of some way to factor it out.  
* Why are those `all`, `dist` and so on targets needed, and why does CI change from building `tools/boostdep/build` to `tools/boostdep//dist`?  
* Why are you adding `link=static` to my CI?  
* Why is it necessary to move the install rules from build/ to a root build.jam?

---

## Comment 2

> Username: grafikrobot  
> Created_at: 2024-05-01 18:15:41 UTC  
> Url: https://github.com/boostorg/boostdep/pull/21#issuecomment-2088871008  

> You'll have to walk me through these changes and explain why each is needed.  
>   
>     * I don't think I ever plan to have a `modular` branch so I don't need the CI changes.  
  
As the PR is tied to the branch I'm developing in I need those changes. As otherwise there's no way for me to test that things are working as expected and without regressions. They are harmless though. :-)  
  
>     * I don't like `explicit [ this ] [ that ] ;` and prefer the old school `this ; explicit this ; that ; explicit that ;`  
  
Okay. I was being consistent with all the other changes in libs/tools I'm making. But I can change it to be old-skool.  
  
>     * The repetition of the DIST_DIR logic in every repo doesn't strike me as elegant. We should try to think of some way to factor it out.  
>   
>     * Why are those `all`, `dist` and so on targets needed, and why does CI change from building `tools/boostdep/build` to `tools/boostdep//dist`?  
  
To be consistent I'm adding an "all", and other targets. I still need to write up an explanation of those in README.md though.  
  
>     * Why are you adding `link=static` to my CI?  
  
I can probably remove that. It was just easier to debug building when I didn't have to worry about DLL linking at one point.  
  
>     * Why is it necessary to move the install rules from build/ to a root build.jam?  
  
To be notionally consistent with the project root declaring *public* targets. Hence equivalently to cmake.

---

## Comment 3

> Username: grafikrobot  
> Created_at: 2024-05-01 18:27:25 UTC  
> Url: https://github.com/boostorg/boostdep/pull/21#issuecomment-2088886615  

Forgot to answer this one..  
  
>     * The repetition of the DIST_DIR logic in every repo doesn't strike me as elegant. We should try to think of some way to factor it out.  
  
It's not, but it works. But it's also now better than it used to be. As before it was done different for each of the tools. Ideally we should use the https://www.bfgroup.xyz/b2/manual/release/index.html#bbv2.builtin.features.install-prefix features. But I didn't want to go risking breaking things more than I had to.

---

## Comment 4

> Username: pdimov  
> Created_at: 2024-05-01 18:34:56 UTC  
> Url: https://github.com/boostorg/boostdep/pull/21#issuecomment-2088896553  

If we copy-paste the same DISTDIR logic everywhere, it will eventually deviate and we'll be back to status quo.

---

## Comment 5

> Username: pdimov  
> Created_at: 2024-05-01 18:36:34 UTC  
> Url: https://github.com/boostorg/boostdep/pull/21#issuecomment-2088898661  

> As the PR is tied to the branch I'm developing in I need those changes.  
  
You can keep these in your fork; I don't need them here.

---

## Comment 6

> Username: grafikrobot  
> Created_at: 2024-05-02 14:07:41 UTC  
> Url: https://github.com/boostorg/boostdep/pull/21#issuecomment-2090589850  

> > As the PR is tied to the branch I'm developing in I need those changes.  
>   
> You can keep these in your fork; I don't need them here.  
  
I reworked the logic to avoid mentioning "modular" except for the single branch filter at the top. We can remove that single mention in a future change.

---

## Comment 7

> Username: grafikrobot  
> Created_at: 2024-05-02 14:08:33 UTC  
> Url: https://github.com/boostorg/boostdep/pull/21#issuecomment-2090591744  

> > ```  
> > * I don't like `explicit [ this ] [ that ] ;` and prefer the old school `this ; explicit this ; that ; explicit that ;`  
> > ```  
>   
> Okay. I was being consistent with all the other changes in libs/tools I'm making. But I can change it to be old-skool.  
  
Did that.

---

## Comment 8

> Username: grafikrobot  
> Created_at: 2024-05-02 23:29:45 UTC  
> Url: https://github.com/boostorg/boostdep/pull/21#issuecomment-2091904698  

> > ```  
> > * Why are you adding `link=static` to my CI?  
> > ```  
>   
> I can probably remove that. It was just easier to debug building when I didn't have to worry about DLL linking at one point.  
  
That's also done. Anything else?

---

## Comment 9

> Username: pdimov  
> Created_at: 2024-10-10 15:34:18 UTC  
> Url: https://github.com/boostorg/boostdep/pull/21#issuecomment-2405452309  

This still contains changes to ci.yml that I'm not willing to accept.

---

## Comment 10

> Username: grafikrobot  
> Created_at: 2025-04-05 02:57:39 UTC  
> Url: https://github.com/boostorg/boostdep/pull/21#issuecomment-2780160745  

@pdimov in case you didn't notice, I removed the ci.yml changes some time ago.

---

## Comment 11

> Username: pdimov  
> Created_at: 2025-04-05 12:13:34 UTC  
> Url: https://github.com/boostorg/boostdep/pull/21#issuecomment-2780690017  

The "check exceptions" CI job fails, because it uses `b2 tools/boostdep/build` to build `boostdep` and then tries to run it as `dist/bin/boostdep`, but it isn't there.

---
