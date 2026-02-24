# #30 Add appveyor and travis pull request integration [Merged]

> Username: jeking3  
> Created at: 2017-09-08 22:44:09 UTC  
> Updated at: 2017-10-18 19:49:10 UTC  
> Merged at: 2017-09-10 20:22:14 UTC  
> Closed at: 2017-09-10 20:22:14 UTC  
> Url: https://github.com/boostorg/uuid/pull/30  

Also includes an appveyor environment patch for boostorg/serialization  
that allows it to build, which will be submitted back to that project.  
  
The workflow to use this is to first fork uuid into your own github account and then follow the instructions located here:  
  
https://svn.boost.org/trac10/wiki/TravisCoverals  
  
Then when you make a code change, you first submit it as a pull request to your own fork's develop and then the Appveyor and Travis CI integration will kick in and build things!  Then when you are satisfied you can make another pull request against boostorg/uuid:develop and as long as it has the same commit ID, I believe that means it will also get the build status from the CI providers.  We'll see, this is all new territory for me.  
  
I would vastly prefer it if pull requests going into boostorg/uuid either master or develop would get automatic Appveyor and Travis CI builds, but that would require whomever has admin access to those accounts to set it up.  
  
I also added coveralls integration but it turns out uuid has no source code - it's all templates, so I get no coverage data.  That makes me quite sad...  
  
Anyway when the branch/account is properly set up here's what you get:  
  
https://github.com/jeking3/uuid/pull/1  
  
This PR produced the following clean builds:  
  
https://travis-ci.org/jeking3/uuid/builds/273622379  
https://ci.appveyor.com/project/jeking3/uuid/build/1.0.22-develop

---

## Comment 1

> Username: eldiener  
> Created_at: 2017-09-08 23:51:26 UTC  
> Url: https://github.com/boostorg/uuid/pull/30#issuecomment-328238822  

You can open a GitHub boost/admin issue if you like.

---

## Comment 2

> Username: jeking3  
> Created_at: 2017-09-09 00:17:44 UTC  
> Updated_at: 2017-09-09 08:14:23 UTC  
> Url: https://github.com/boostorg/uuid/pull/30#issuecomment-328241136  

@eldiener I'm so glad you pointed me at that - I'll try not to flood your board with issues. :)  
  
It is not "my board" but Boost's :) and boostorg/admin issues are for what you want when you need to have administration things taken care of.

---

## Comment 3

> Username: jeking3  
> Created_at: 2017-09-09 03:13:51 UTC  
> Updated_at: 2017-09-09 03:15:29 UTC  
> Url: https://github.com/boostorg/uuid/pull/30#issuecomment-328250600  

@eldiener, @Lastique I'd appreciate a code review, although no code really changed, I would like to get this merged into uuid and I want to make sure everything is okay since I'm fairly new at boost committing.  Thanks!  
  
By the way, the serialization patch is temporary, just enough to get tests to pass here; I submitted a PR into serialization but there are a lot more issues with mingw, and when I applied the full compiler suite to serialization:develop nothing built.  That project needs some TLC?

---

## Comment 4

> Username: jeking3  
> Created_at: 2017-09-09 20:21:56 UTC  
> Url: https://github.com/boostorg/uuid/pull/30#issuecomment-328301288  

I wasn't able to get valgrind integration working, but I'll look into it in the future.

---
