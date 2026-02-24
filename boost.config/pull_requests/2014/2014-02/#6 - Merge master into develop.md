# #6 Merge master into develop. [Closed]

> Username: ahmedcharles  
> Created at: 2014-02-11 09:55:31 UTC  
> Updated at: 2014-02-13 10:57:47 UTC  
> Closed at: 2014-02-13 10:57:47 UTC  
> Url: https://github.com/boostorg/config/pull/6  

Both master and develop are currently the same, so they should effectively have each other as parents to make future merges easier.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2014-02-11 12:50:51 UTC  
> Url: https://github.com/boostorg/config/pull/6#issuecomment-34751039  

Sorry I don't understand what is this supposed to do?

---

## Comment 2

> Username: ahmedcharles  
> Created_at: 2014-02-13 02:11:04 UTC  
> Url: https://github.com/boostorg/config/pull/6#issuecomment-34941511  

Well, if you look at the following page, which describes git flow (which we are supposed to be using but as far as I can tell, very few people are): http://nvie.com/posts/a-successful-git-branching-model/  
  
It says: "To keep the changes made in the release branch, we need to merge those back into develop, though."  
  
That's the most concise explanation I can provide, since this change is effectively doing that.

---

## Comment 3

> Username: eldiener  
> Created_at: 2014-02-13 04:25:02 UTC  
> Url: https://github.com/boostorg/config/pull/6#issuecomment-34947346  

```  
On 2/12/2014 9:11 PM, Ahmed Charles  
  wrote:  
  
  Well, if you look at the following page, which describes git  
    flow (which we are supposed to be using but as far as I can  
    tell, very few people are): http://nvie.com/posts/a-successful-git-branching-model/  
  It says: "To keep the changes made in the release branch, we  
    need to merge those back into develop, though."  
  That's the most concise explanation I can provide, since this  
    change is effectively doing that.  
  —  
    Reply to this email directly or view  
      it on GitHub.  
  
Why would changes be made in release before they are made, and  
tested, in develop ?  
```

---

## Comment 4

> Username: ahmedcharles  
> Created_at: 2014-02-13 05:45:35 UTC  
> Url: https://github.com/boostorg/config/pull/6#issuecomment-34950357  

In git flow, that's how you do hotfixes. But more commonly, you aren't supposed to merge from develop to master directly, there's supposed to be an intermediate branch which is used to stabilize the release before it merges to master. Those changes get merged to develop due to this step. Note, you can merge from a release or hotfix branch to develop after each commit, the only relevant thing is that the history reflect the fact that the fix is in both branches and that the fix is indeed, the same commit hash. This implies that cherry-picking is suboptimal, since the link between the fix on develop and master isn't obvious. This results in situations like the current state of many boost libraries where no one can tell which changes have been merged to master and which haven't.  
  
As a slight meta-point, the model boost used with svn was one in which changes to trunk and release were not strongly related, therefore the history of boost when viewed through git is fairly useless, since it simply doesn't look like a git project. Git flow's entire purpose is to create a history graph that is consistent and makes sense. If all you need is to answer questions like "who changed this line and when?" then git flow is overkill. I suspect that there will be lots of growing pains due to this, especially once people start to make more complex changes.

---

## Comment 5

> Username: eldiener  
> Created_at: 2014-02-13 06:46:08 UTC  
> Url: https://github.com/boostorg/config/pull/6#issuecomment-34952633  

```  
On 2/13/2014 12:45 AM, Ahmed Charles  
  wrote:  
  
  In git flow, that's how you do hotfixes. But more commonly, you  
    aren't supposed to merge from develop to master directly,  
    there's supposed to be an intermediate branch which is used to  
    stabilize the release before it merges to master. Those changes  
    get merged to develop due to this step. Note, you can merge from  
    a release or hotfix branch to develop after each commit, the  
    only relevant thing is that the history reflect the fact that  
    the fix is in both branches and that the fix is indeed, the same  
    commit hash. This implies that cherry-picking is suboptimal,  
    since the link between the fix on develop and master isn't  
    obvious. This results in situations like the current state of  
    many boost libraries where no one can tell which changes have  
    been merged to master and which haven't.  
  As a slight meta-point, the model boost used with svn was one  
    in which changes to trunk and release were not strongly related,  
    therefore the history of boost when viewed through git is fairly  
    useless, since it simply doesn't look like a git project. Git  
    flow's entire purpose is to create a history graph that is  
    consistent and makes sense. If all you need is to answer  
    questions like "who changed this line and when?" then git flow  
    is overkill. I suspect that there will be lots of growing pains  
    due to this, especially once people start to make more complex  
    changes.  
  —  
    Reply to this email directly or view  
      it on GitHub.  
  
I still feel it is always wrong to put a change into release before  
it is put in develop and tested.  
```

---

## Comment 6

> Username: ahmedcharles  
> Created_at: 2014-02-13 06:59:06 UTC  
> Url: https://github.com/boostorg/config/pull/6#issuecomment-34953140  

Two things:  
  
1) This change is reasonable and doesn't hurt anything, nor does it imply that things need to happen a specific way in the future.  
2) With regard to testing, I don't see how develop is necessary to have regression testing happen, even if that's the current state of the world.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2014-02-13 10:57:47 UTC  
> Url: https://github.com/boostorg/config/pull/6#issuecomment-34967973  

OK I see the point of that, I've created a merge point from release->develop, I didn't use your pull request simply because I couldn't fathom what the diff was telling me :(

---
