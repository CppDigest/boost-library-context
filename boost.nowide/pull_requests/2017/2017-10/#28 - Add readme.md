# #28 Add readme [Merged]

> Username: Flamefire  
> Created at: 2017-10-01 22:53:02 UTC  
> Updated at: 2019-12-30 12:07:44 UTC  
> Merged at: 2019-12-30 12:07:40 UTC  
> Closed at: 2019-12-30 12:07:40 UTC  
> Url: https://github.com/boostorg/nowide/pull/28  

This adds a simple readme that provides a short description and (most importantly) a link to the online docu.

---

## Comment 1

> Username: artyom-beilis  
> Created_at: 2017-10-02 07:23:49 UTC  
> Url: https://github.com/boostorg/nowide/pull/28#issuecomment-333458600  

Thank you for so many good patches.  
  
I'm not ignoring you I'm little bit busy right now. I'll go over all of them and do relevant merges.  
  
Thanks.

---

## Comment 2

> Username: hogarth-sv  
> Created_at: 2017-10-03 08:42:52 UTC  
> Url: https://github.com/boostorg/nowide/pull/28#issuecomment-333778049  

@Flamefire Just wanted to say thank you for this. It's currently an embedded dependency in the current version of Puppetlabs Facter and I'd really like to have that unbundled for the updated Fedora package, and have this packaged on it's own in Fedora. Having the update CMake files for the shared library creation is essential for that goal.  
  
@artyom-beilis For the purposes of the Fedora review request I'll be using his patches to get a shared library built but it'd be great to have that patch merged upstream here.

---

## Comment 3

> Username: Flamefire  
> Created_at: 2019-12-30 12:07:34 UTC  
> Url: https://github.com/boostorg/nowide/pull/28#issuecomment-569660751  

@hogarthj As you may have noticed I took over maintenance of this project. Together with Peter Dimov I created updated CMakeFiles which will also integrate with the future Boost.CMake based build. But I'll also see that this can be used as a subproject from within CMake given an installed Boost. Those shouldn't be mixed and are recognizable by their versioning as the Boost installed version will follow the 1.x.y convention while the sub-project/semi-standalone one will use 10.x.y  
  
I have a couple bugfixes in the pipeline and am integrating them now while making sure not to break anything. So watch develop for being merged into master which I'll do once I got the patches done :)

---
