# #662 - update master branch with existing fixes from develop branch [Closed]

> Username: striezel  
> Created at: 2022-05-05 12:25:55 UTC  
> Updated at: 2022-05-05 18:18:42 UTC  
> Closed at: 2022-05-05 18:18:42 UTC  
> Url: https://github.com/boostorg/gil/issues/662  

### Is your feature request related to a problem? Please describe.  
  
Basically, there should be a merge of develop into master to get the newer fixes onto master and possibly into one of the next Boost releases.  
  
For example, as of now (2022-05-05) those fixes are currently on develop but not on master yet, just to name some:  
  
* #579  
* #617  
* #621  
* #650  
  
Currently, the master branch is more than 100 commits behind develop, so there is a lot more to merge than the mentioned issue fixes.  
  
### Describe the solution you'd like  
  
* Simply put: `git checkout master && git merge develop` should do it, but there are merge conflicts. So it needs the expertise of somebody like @mloskot who is very familiar with the code base to resolve these conflicts properly.  
* Alternatively: Cherry pick fixes from develop onto master.  
  
### Additional context  
  
The apparent need for an update has already been mentioned in https://github.com/boostorg/gil/pull/658#issuecomment-1116971475:  
  
> @mloskot It looks like `master` needs an update...  
  
and  
  
> @sdebionne Yes, I've neglected the master and not updated it from the develop for a while. I'll do it tonight.  
  
Since there seems to be no further discussion about it (yet;)), I've created this issue as a place for discussion and to get things rolling.  
  
I am not sure what the merge policy / release policy for Boost.GIL is, but I guess there still needs to be some kind of documentation updates (for example: changelog) before the merge can happen.

---

## Comment 1

> Username: sdebionne  
> Created at: 2022-05-05 16:07:28 UTC  
> Url: https://github.com/boostorg/gil/issues/662#issuecomment-1118749661  

Thanks for opening this issue and summing up the situation. I guess we could start with merging MRs that fixe issues tagged with   
  
- cat/annoyance  
- cat/bug  
  
IMHO, new features that lack documentations, such as the image processing GSOCs / Numerical Toolbox, should stay in develop.

---

## Comment 2

> Username: striezel  
> Created at: 2022-05-05 18:05:56 UTC  
> Url: https://github.com/boostorg/gil/issues/662#issuecomment-1118893038  

But even for those annoyance fixes / bug fixes there should probably be some kind of documentation. So what is the usual procedure for that? I am asking so that maybe someone can already prepare a bit of that as a PR.
