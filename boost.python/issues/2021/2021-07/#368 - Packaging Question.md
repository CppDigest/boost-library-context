# #368 - Packaging Question [Open]

> Username: fishbotics  
> Created at: 2021-07-12 17:25:36 UTC  
> Updated at: 2022-03-28 18:19:11 UTC  
> Url: https://github.com/boostorg/python/issues/368  

Hi all,  
  
I am using a library built on Boost.Python as a dependency for some of my other projects. This library (https://github.com/ompl/ompl) requires building from source and is always linked to the system Python. I would like to make it installable via pip and distribute it on PyPi. My biggest question around this is how to handle Boost.Python. When I look at the generated shared object files, they are linked to a Python-version-specific version of Boost. Namely, I see  
  
```  
libboost_python38.so.1.71.0 => /usr/lib/x86_64-linux-gnu/libboost_python38.so.1.71.0 (0x00007f9d6d9b7000)  
```  
I'm wondering what is the best practice for distributing a pre-built package like this? Should I statically link to a particular version of Boost.Python? Assuming I only handle Linux support to start, I don't mind asking them to use `apt` to install Boost. But, I'd like to enable support across multiple Python versions. If, for example, they are running Ubuntu 20.04, which has system Python version of 3.8, libboost-python-dev seems to install the 3.8-specific version. Would I have to include the entire boost source code and build from scratch when they run `pip install`?  
  
I'd love to learn by example, but I haven't been able to find any PyPi-distributed packages that use Boost.Python. Can anyone point me to one?  
  
Thanks a lot for your help!

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2021-07-12 17:35:21 UTC  
> Url: https://github.com/boostorg/python/issues/368#issuecomment-878464155  

Hi Adam,  
I'm not convinced that trying to package and distribute such code with `pip` is a good idea, as PyPI and distribution-specific package managers don't know about each other, yet your PyPI package depends on libraries that are also available through system packages.  
Have you considered using Anaconda / conda to manage your packages ? I know conda was designed specifically to deal with this kind of situation, i.e. its encapsulation of Python environments (and associated package dependency graphs) is much more powerful than that of pip / PyPI.

---

## Comment 2

> Username: fishbotics  
> Created at: 2021-07-12 17:43:04 UTC  
> Updated at: 2021-07-12 17:46:07 UTC  
> Url: https://github.com/boostorg/python/issues/368#issuecomment-878468831  

Hi Stefan,  
  
I am open to distributing through conda instead of PyPi, but it's not my top choice. I've used Conda quite a bit as a consumer (not as a package creator) and I'm always frustrated by the difficulty of managing dependencies through it. Some of the packages I use are only distributed through PyPI and inevitably, the conda solver breaks and only provides limited information on how to fix the situation. Because of many lost hours trying to fix broken environments, I recently switched to managing my dev environments through Poetry and exclusively using Pip (through Poetry's solver). It's been a total game-changer in terms of creating reproducible dev environments.  
  
That said, if it's way easier to do this kind of thing from conda, I will gladly switch back. It's all about cost vs. benefit for me and having this library be easily installable for future work would be a huge benefit :)  
  
Do you have any suggestions on how to get started packaging a library with Boost.Python for conda?  
  
[EDIT] I see you were a developer for Continuum! I hope nothing I said came off as rude :man_facepalming:. I think it's a great product--I've just run into the edges a few too many times

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2021-07-12 17:59:00 UTC  
> Url: https://github.com/boostorg/python/issues/368#issuecomment-878478811  

I suppose the difficulties you encountered with conda stem from the complexity of the problem, not the implementation.  
If you want to distribute Boost.Python with your package, you need to make sure the installation is well encapsulated in a virtual environment so as not to conflict with the system-level Boost.Python library. However, pip  does not provide any such guarantees. (In fact, isn't that one of the main selling points of conda ?)  
On the other hand, you can't rely on the system-level Boost.Python installation, as pip has no knowledge of packages managed by something other than PyPI.  
  
I don't have any specific examples, unfortunately, but I'm sure you are not the first person wanting to distribute a Python package that requires Boost.Python. :-)

---

## Comment 4

> Username: fishbotics  
> Created at: 2021-07-12 18:08:34 UTC  
> Url: https://github.com/boostorg/python/issues/368#issuecomment-878485749  

This all makes sense. I'll do some searching around for Conda examples and see what I can find.

---

## Comment 5

> Username: AllSeeingEyeTolledEweSew  
> Created at: 2021-09-04 07:00:57 UTC  
> Url: https://github.com/boostorg/python/issues/368#issuecomment-912922953  

> I'm not convinced that trying to package and distribute such code with `pip` is a good idea, as PyPI and distribution-specific package managers don't know about each other, yet your PyPI package depends on libraries that are also available through system packages.  
  
This reflects a pattern of @boostorg dismissing the python ecosystem. I'm very frustrated by this pattern.  
  
`pip` is the officially-supported installation tool. pypi is the officially-supported standard package ecosystem. Shipping binary wheels on pypi is officially-supported and common.  See [cryptography](https://pypi.org/project/cryptography/) or [numpy](https://pypi.org/project/numpy/) for examples. Static linking is a thing; `manylinux` is *one of* the officially-supported standards for how to do it.  
  
Yet boost.python and boost.build make it difficult to follow officially-supported python standards. boost.build offers no integration with `setuptools` or `build`, and has a host of issues that make it hard to roll the integration yourself (https://github.com/boostorg/build/issues/706). boost.python makes no attempt to conform to `Py_LIMITED_API` (#221), which means multiplying the already-huge compilation times of boost, due to building artifacts for each python version.  
  
In the threads I linked above, there's been simply no response from @boostorg. In this thread, the response is to dismiss official standards rather than trying to solve the problem.  
  
This might be fine, if @boostorg provided guidance for packaging and distribution outside the norms of the python ecosystem, but I've seen no such guidance in months of searching.  
  
I know I risk being rude here. But users are already making noise about packaging issues, and there has been no recognition, so I believe that additional noise is warranted.

---

## Comment 6

> Username: AllSeeingEyeTolledEweSew  
> Created at: 2021-09-04 07:06:06 UTC  
> Url: https://github.com/boostorg/python/issues/368#issuecomment-912923878  

@fishbotics: check out https://github.com/arvidn/libtorrent/blob/RC_2_0/bindings/python/setup.py . We integrate `setuptools` with b2 and boost.python. It's a lot of work, but it integrates with the rest of the python build ecosystem, so we can build wheels. From there, `cibuildwheel` is a big help to make wheels suitable for publishing to pypi.  
  
It looks like the project you linked is cmake-based, so you wouldn't need a lot of the logic, but you could still use it as a starting point.

---

## Comment 7

> Username: fishbotics  
> Created at: 2022-03-28 18:19:11 UTC  
> Url: https://github.com/boostorg/python/issues/368#issuecomment-1080988676  

Thank you @AllSeeingEyeTolledEweSew. I appreciate your fervor, even if it may seem rude to @boostorg. For now, I ended up just throwing all my dev work using this library into a docker container and using that as "packaging." It's a terrible solution, but has been a reasonable temporary band-aid. I'll take a look at the repo you linked and try to piece something together.
