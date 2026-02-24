# #644 Drone config [Merged]

> Username: sdarwin  
> Created at: 2021-02-03 17:09:43 UTC  
> Updated at: 2021-02-09 16:26:57 UTC  
> Merged at: 2021-02-09 13:08:22 UTC  
> Closed at: 2021-02-09 13:08:22 UTC  
> Url: https://github.com/boostorg/spirit/pull/644  

Drone is a continuous integration framework similar to Travis CI. [The C++ Alliance](https://cppalliance.org/) is offering a hosted Drone server for Boost libraries. Please refer to https://github.com/CPPAlliance/drone-ci for more information and instructions.

---

## Review 1 [Commented]

> Username: Kojoley  
> Created_at: 2021-02-04 17:53:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/644#pullrequestreview-583662985  

📁 .travis.yml

```diff
 119 |       : unchecked ;
 120 | 
 121 |+     EOF
```

> Username: Kojoley  
> Created_at: 2021-02-04 17:53:36 UTC  
> Updated_at: 2021-02-08 21:16:24 UTC  
> Url: https://github.com/boostorg/spirit/pull/644#discussion_r570427230  

IIRC this will fail travis, please remove.


---

## Comment 2

> Username: sdarwin  
> Created_at: 2021-02-04 18:47:26 UTC  
> Url: https://github.com/boostorg/spirit/pull/644#issuecomment-773526426  

@Kojoley  , you may be right.  Testing it now.  Since travis is slow, the result could take a while.   The reason for adding the EOF, is because in a typical bash script, you can't usually open a EOF section without also closing it.   Converting the same travis script to any other CI system will probably require this.   So it would be convenient to add it in travis.yml, which can act as a reference implementation.  Let's see how the test goes.

---

## Comment 3

> Username: sdarwin  
> Created_at: 2021-02-04 20:00:21 UTC  
> Url: https://github.com/boostorg/spirit/pull/644#issuecomment-773568601  

Change reverted. https://travis-ci.community/t/multiline-commands-have-two-spaces-in-front-breaks-heredocs/2756

---

## Review 4 [Commented]

> Username: Kojoley  
> Created_at: 2021-02-07 17:50:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/644#pullrequestreview-585038769  

📁 .drone/drone.sh

```diff
  70 |+ else
  71 |+   # It is a pull request. Retrieve the base branch from GitHub
  72 |+   GH_PR_API=https://api.github.com/repos/$TRAVIS_REPO_SLUG/pulls/$TRAVIS_PULL_REQUEST
```

> Username: Kojoley  
> Created_at: 2021-02-07 17:50:12 UTC  
> Updated_at: 2021-02-08 21:16:24 UTC  
> Url: https://github.com/boostorg/spirit/pull/644#discussion_r571653451  

These were not defined from Drone alternatives.


---

## Comment 5

> Username: Kojoley  
> Created_at: 2021-02-07 18:02:26 UTC  
> Url: https://github.com/boostorg/spirit/pull/644#issuecomment-774722585  

Is Drone not running because you specified `[ci skip]` in the commit message? I login into the site and someone seems to be already enabled the repository, but build list is empty.  
  
I see you are using boost-ci (I am not fan of that) which seems to set/override build triggers to the particular set of predefined branches what is a major PITA for me from the experience with other boost libraries/repos.  
  
Also a question, does Drone run for forks? It is not that much of a deal for this repository as I can create a branch, but for other boost repositories I usually test my changes before opening a PR because sometime they are experimental and will not result in a PR at all.

---

## Comment 6

> Username: sdarwin  
> Created_at: 2021-02-08 21:11:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/644#issuecomment-775464766  

> $TRAVIS_REPO_SLUG/pulls/$TRAVIS_PULL_REQUEST  
  
I will push a new commit here to fix it.  
  
> Is Drone not running because you specified [ci skip] in the commit message?   
  
That would prevent it from running, yes.    
Also, for Drone to run the first time you must go through all the steps (not very many) in the instructions section of https://github.com/CPPAlliance/drone-ci  
  
> which seems to set/override build triggers to the particular set of predefined branches   
  
triggers are now a variable. In your .drone.star, at the top of the file, add a value such as this:  
```  
globaltriggers={ "branch": [ "master", "develop", "drone*", "bugfix/*", "feature/*", "fix/*", "pr/*" ] }  
```  
Modify globaltriggers as desired. Then, in each individual job set an argument triggers=globaltriggers to use that value.  
  
> does Drone run for forks?   
  
Drone is being restricted to a whitelist. Kojoley is now in the list so you may set up jobs from your forks.

---

## Comment 7

> Username: djowel  
> Created_at: 2021-02-09 00:58:25 UTC  
> Url: https://github.com/boostorg/spirit/pull/644#issuecomment-775572681  

@Kojoley Tell us if we're good, and I'll merge.

---

## Comment 8

> Username: Kojoley  
> Created_at: 2021-02-09 13:11:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/644#issuecomment-775924837  

> Drone is being restricted to a whitelist. Kojoley is now in the list so you may set up jobs from your forks.  
  
Much appreciated, and thanks for the PR.

---

## Comment 9

> Username: sdarwin  
> Created_at: 2021-02-09 15:53:56 UTC  
> Url: https://github.com/boostorg/spirit/pull/644#issuecomment-776043246  

to avoid that EOF problem, it might be a good idea to copy the file, rather than generate it.   Put a copy of the file in a scripts/, tools/ or ci/ directory.  
  
Here is what boostorg/beast does:  
  
```  
cp libs/beast/tools/user-config.jam ~/user-config.jam  
echo "using $TOOLSET : : $COMPILER : $CXX_FLAGS ;" >> ~/user-config.jam  
```

---

## Comment 10

> Username: Kojoley  
> Created_at: 2021-02-09 16:26:57 UTC  
> Url: https://github.com/boostorg/spirit/pull/644#issuecomment-776063856  

Thanks for the suggestion, but I prefer having everything related to a particular CI in its configuration file for visibility.

---
