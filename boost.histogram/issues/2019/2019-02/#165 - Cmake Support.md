# #165 - Cmake Support [Closed]

> Username: triskeldeian  
> Created at: 2019-02-25 19:41:59 UTC  
> Updated at: 2019-04-14 20:35:52 UTC  
> Closed at: 2019-04-14 20:35:52 UTC  
> Url: https://github.com/boostorg/histogram/issues/165  

Dear Hans,  
I was evaluating your project and I think it's really interesting but I wonder why you decided to remove the CMake support and whether that will return in the near future. I thought even Boost planned to move to CMake

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-02-26 13:47:46 UTC  
> Updated at: 2019-02-26 13:48:20 UTC  
> Url: https://github.com/boostorg/histogram/issues/165#issuecomment-467444103  

Dear @caiazza ,sorry about that. I am currently not testing the cmake support and I don't want untested code to be in the release. And I am running out of time to make changes before the release. The cmake support will come back at a later point.

---

## Comment 2

> Username: HDembinski  
> Created at: 2019-02-26 13:49:10 UTC  
> Url: https://github.com/boostorg/histogram/issues/165#issuecomment-467444616  

Just out of curiosity, why do you need cmake support for a header-only library and how do you use it? For running the tests, b2 is quite convenient.

---

## Comment 3

> Username: triskeldeian  
> Created at: 2019-02-26 14:14:46 UTC  
> Url: https://github.com/boostorg/histogram/issues/165#issuecomment-467453916  

I understand perfectly your priorities.  
As for me I'm looking forward to the time when I could use just build selected parts of Boost as an external_project directly, which I do for other external dependencies, even those that are header only. In those cases it's easier in my opinion to set up an external_project in CMake connected to a specific git tag than drop the sources in. In this way if I want to update to a new version of a library or try a different one I can just change one variable, even at configuration time.  
In the boost case that is even more complicated because of all the internal dependencies which are not even clearly documented in most cases so you never know which other module you need if you want to use a certain library. For example in your case I think you are using MPL, accumulators and iterators but I'm not sure because I cannot find where it is actually written, unless, I suppose, I go browsing the JAMFILE.  
Actually, triggered by the curiosity over your project I discovered bcp which at least would allow me to package only what's strictly necessary and drop it in my project, which is already good enough in this case.

---

## Comment 4

> Username: triskeldeian  
> Created at: 2019-02-26 14:18:08 UTC  
> Url: https://github.com/boostorg/histogram/issues/165#issuecomment-467455123  

Anyway, I really like your project and I look forward to use it as I think it would be extremely useful.

---

## Comment 5

> Username: HDembinski  
> Created at: 2019-02-26 14:52:31 UTC  
> Url: https://github.com/boostorg/histogram/issues/165#issuecomment-467468075  

I am glad you like the library, please star the project if you haven't done so :).  
  
You raise good points regarding using only selected parts of boost and the non-well documented inter-dependencies of boost libraries.  
Figuring out the dependencies is difficult, because a library that histogram needs may itself depend on other boost libraries. We internally use tools to figure this out, but these are not perfect, since they do not apply preprocessing of the headers. So if you have an optional dependency where something is only included when you set a flag, these tools currently do not understand that. Nevertheless you might find that useful. One tool you can try is `boost-root/tools/boostdep/depinst/depinst.py`, try `depinst.py -h` for help. Basically, you download the boost super-project via git and then use this tool on histogram to download the histogram dependencies (which currently are a lot, mostly because some of my dependencies pull in large amounts of other boost libs). To get an idea what is depending on what, you can also compile the boostdep tool in `boost-root/tools/boostdep`, which provides more options. It has a CMakeLists.txt, so should be cmake-installable. I hope this helps.

---

## Comment 6

> Username: triskeldeian  
> Created at: 2019-02-26 15:13:04 UTC  
> Url: https://github.com/boostorg/histogram/issues/165#issuecomment-467476640  

Thanks for the advice. Seems very similar to what bcp is supposed to do. i'll give it a try as soon as I have a bit of time to plug in your lib and test it properly

---

## Comment 7

> Username: HDembinski  
> Created at: 2019-02-26 15:19:33 UTC  
> Url: https://github.com/boostorg/histogram/issues/165#issuecomment-467479465  

If you want a working example of depinst.py, checkout the .travis.yml. I googled BCP now, I wasn't aware of it. depinst.py is used by many boost libs internally to download the dependencies for CI testing.

---

## Comment 8

> Username: henryiii  
> Created at: 2019-03-07 10:49:13 UTC  
> Url: https://github.com/boostorg/histogram/issues/165#issuecomment-470478909  

There will be an example of this in boost-histogram for Python, it has a CMake file (and will have a setup.py file too soon). At the moment, it only selects header only parts of Boost, so should support everything except serialization. Everything is loaded with git submodules. Would a separate package that simply adds all the needed submodules, and provides a CMake file to export the target with all the includes, be a useful way to break out that functionality?  
  
I'll link to the python package once it gets moved to SciKit-HEP.

---

## Comment 9

> Username: triskeldeian  
> Created at: 2019-03-12 08:44:29 UTC  
> Url: https://github.com/boostorg/histogram/issues/165#issuecomment-471907937  

I think that rather than a separate package a simple CMake macro that does that will be enough so that if necessary one can just copy that macro in place and solve the issue

---

## Comment 10

> Username: HDembinski  
> Created at: 2019-03-12 09:34:16 UTC  
> Url: https://github.com/boostorg/histogram/issues/165#issuecomment-471924043  

> Would a separate package that simply adds all the needed submodules, and provides a CMake file to export the target with all the includes, be a useful way to break out that functionality?  
  
This is not a good solution. Such a repo wouldn't be accepted by boostorg, and it also takes attention away from the main repo. I think the cmake build can just download the dependencies automatically on the first run, like @caiazza suggests. See https://github.com/boostorg/histogram/issues/163 on how that could work.

---

## Comment 11

> Username: HDembinski  
> Created at: 2019-04-14 20:35:52 UTC  
> Url: https://github.com/boostorg/histogram/issues/165#issuecomment-483056165  

Limited CMake support is back in the develop branch.
