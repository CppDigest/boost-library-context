# #63 Add Travis-ci as the continuous integration service for Boost.Graph [Merged]

> Username: maelvls  
> Created at: 2016-04-26 09:57:53 UTC  
> Updated at: 2016-10-31 19:22:40 UTC  
> Merged at: 2016-10-31 19:22:40 UTC  
> Closed at: 2016-10-31 19:22:40 UTC  
> Url: https://github.com/boostorg/graph/pull/63  

I thought it would be a great idea to have the test running from a  
"neutral entity" instead of having to checkout the pull request and  
  
```  
cd libs/graph/test && ../../../b2  
```  
  
I saw that many other boostorg submodules had their own CI system:  
- compute relies on travis-ci  
- geometry uses circleci  
  
The major difference between circleci and travis-ci is the multi-threading supported by circleci. Both are free for open-source project (as of 2016/04/26). I chose travis-ci because I know how to configure it.  
  
As for geometry, we could also use coveralls.io to display the test covering reports and improve the quality/coverage of tests on boostorg/graph.  
  
**IMPORTANT: a member of boostorg will have to add the repo boostorg/graph into travis-ci for it to work.**  
  
I tested this file against [my fork](https://github.com/maelvalais/graph/). Here is the [travis-ci part](https://travis-ci.org/maelvalais/graph/). It works great!  
  
The only slight issue is that it takes ~10min to build and test, but it's totally fine.  
  
Here is what displays when opening a PR:  
  
[![capture d ecran 2016-05-03 a 10 36 16](https://cloud.githubusercontent.com/assets/2195781/14978845/eaf15ee4-111e-11e6-94b9-9601e430182c.png)](https://github.com/maelvalais/graph/pull/1)  
![capture d ecran 2016-05-03 a 11 04 55](https://cloud.githubusercontent.com/assets/2195781/14978865/0b321946-111f-11e6-9a41-991f0bf793c3.png)

---

## Comment 1

> Username: murraycu  
> Created_at: 2016-08-12 07:42:58 UTC  
> Url: https://github.com/boostorg/graph/pull/63#issuecomment-239381920  

I don't see a downside to this.

---
