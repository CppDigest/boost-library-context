# #203 - Discrepancy between python-tag definitions in Boost.Python and superproject [Open]

> Username: Lastique  
> Created at: 2018-05-14 20:32:30 UTC  
> Updated at: 2018-07-03 16:01:43 UTC  
> Url: https://github.com/boostorg/python/issues/203  

In Boost 1.67, Boost.Python uses its own definition of `python-tag` rule in `libs/python/Jamfile`. This rule produces library names like `libboost_python36-py36.so.1.67.0`, notice the 36 after `boost_python`. This is different from Boost 1.66 and from the similar rule defined in the superproject, in `boostcpp.jam`.  
  
In particular, this results in different naming between Boost.Python and Boost.MPI Python binding libraries. I'm not sure why the duplicate Python version is needed, so this issue requests to port Boost.Python to the superproject definition of the rule.

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2018-05-14 20:43:27 UTC  
> Url: https://github.com/boostorg/python/issues/203#issuecomment-388956237  

Indeed, I have modified the `python-tag` to include the Python version suffix in the library name. which now becomes "boost_python36" (for Python 3.6). I have no idea where the "-py36" tag is added; it's certainly not coming from `libs/python/Jamfile`.  
I have added this to `Boost.Python` itself as it also required changes to the `config.hpp` file responsible for auto-linking. If `Boost.MPI` wants to switch to the same naming scheme, we could indeed refactor this logic and move it into the upstream `boost` repo, though that would be an issue for that project, not `Boost.Python`. (Once that's done, I'm happy to adjust my own code as redundant.)

---

## Comment 2

> Username: Lastique  
> Created at: 2018-05-14 20:53:53 UTC  
> Url: https://github.com/boostorg/python/issues/203#issuecomment-388959292  

I suspect, `-py36` is coming from https://github.com/boostorg/python/blob/develop/Jamfile#L63.  
  
I think, the naming rules for Python-related libraries need to be the same to help downstream projects. As I understand it, the superproject's rule is there for exactly that. If you wish to change the naming scheme, I think it is better to work towards updating the common rule instead of creating a duplicate in Boost.Python. I would urge you to propose your change to the superproject.

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2018-05-14 21:09:36 UTC  
> Url: https://github.com/boostorg/python/issues/203#issuecomment-388963741  

I did propose that. In fact, I asked months ago on the main ML whether anyone had an opinion on the matter (no-one had). But as I said, given that this change requires touching three separate repos, it couldn't be done in one atomic change.  
The next step really needs to be to adjust the `Boost.MPI` code (I have no idea whether that requires autolinking changes), and once that's done we can consolidate the change by moving it upstream.

---

## Comment 4

> Username: Lastique  
> Created at: 2018-05-14 21:20:47 UTC  
> Url: https://github.com/boostorg/python/issues/203#issuecomment-388966808  

I don't understand why Boost.MPI needs to change. It has other problems caused by other changes in Boost.Python, see https://github.com/boostorg/mpi/pull/58, but in regard to it using the superproject's `python-tag` rule I think it is doing the right thing. I don't think creating a third copy of `python-tag` is the right way.

---

## Comment 5

> Username: stefanseefeld  
> Created at: 2018-05-14 21:27:23 UTC  
> Url: https://github.com/boostorg/python/issues/203#issuecomment-388968570  

The point is that moving the `python-tag` logic upstream *will* impact Boost.MPI, so we better make sure such a change is desired there.

---

## Comment 6

> Username: Lastique  
> Created at: 2018-05-14 21:31:33 UTC  
> Url: https://github.com/boostorg/python/issues/203#issuecomment-388969720  

@aminiussi Could you comment on this?

---

## Comment 7

> Username: aminiussi  
> Created at: 2018-05-15 06:42:02 UTC  
> Url: https://github.com/boostorg/python/issues/203#issuecomment-389060025  

Ok, but I'm just out from surgery and not familiar with the Python part. So it will take a couple of days.

---

## Comment 8

> Username: aminiussi  
> Created at: 2018-05-20 14:21:30 UTC  
> Url: https://github.com/boostorg/python/issues/203#issuecomment-390486382  

Is this connected to the fact that bjam fails to detect that my python lib and include directory is  tagged as python3.6m, not python3.6 ? (I never used it before)

---

## Comment 9

> Username: stefanseefeld  
> Created at: 2018-05-20 14:50:02 UTC  
> Url: https://github.com/boostorg/python/issues/203#issuecomment-390488099  

No.   
Here we already know the Python instance we are building against. The upstream `python-tag` function was used by (at least) two projects: Boost.Python, and Boost.MPI (its Python bindings). Changing the library name (by way of changing the `python-tag` function) requires changes to the library's autolinking support code, which is why I couldn't just change the upstream function itself.  
Now if you (Boost.MPI) want to implement a similar change to Boost.Python (i.e., appending a Python version suffix directly to the library name), feel free to clone and adapt these commits:  
* https://github.com/boostorg/python/commit/d4d41d94aecc9f8098aabd3587fcb95458451f71 (build logic changes)  
* https://github.com/boostorg/python/commit/24313709a78c694af4ae29116cd06ea86585aa95 (autolink config adustments)  
  
Once that's done, I'll submit a patch upstream to adjust the super-project's `python-tag`, at which point we can both remove our respective customizations (in `$(PROJECT_ROOT)/Jamfile`).  
But just changing the upstream `python-tag` would break all libraries using it, because the name-change needs to be reflected elsewhere in the code (the autolinking stuff, at least).  
  
Makes sense ?

---

## Comment 10

> Username: aminiussi  
> Created at: 2018-05-20 15:42:50 UTC  
> Url: https://github.com/boostorg/python/issues/203#issuecomment-390491326  

Not yet, but it's been 10 years that nothing bjam related made any sense to me :-)  
  
Right now, I'm just trying to build boost python against an anaconda 3.6. It uses the Marangozov allocator that the Boost Python build does not handle.  
  
But it should start making sense at some point.    
  
Thanks for the explanations which should be very useful quite soon I hope.

---

## Comment 11

> Username: stefanseefeld  
> Created at: 2018-05-20 15:59:25 UTC  
> Url: https://github.com/boostorg/python/issues/203#issuecomment-390492327  

@Lastique , to get back to the original report: While I'm happy to contribute my `python-tag` changes to the super-project, those changes (and neither the discrepancies between my version and that of upstream) are responsible for the duplicate tagging in `libboost_python36-py36.so.1.67.0`. Since the inclusion of the Python version suffix into the library name itself is new, I suppose what you are seeing is merely a result of some distribution manager using the `python-id` flag to graft the python version onto the library. Getting rid of this should be as simple as removing that `--python-buildid=py36` option during the build.  
I'm not entirely ready to remove https://github.com/boostorg/python/blob/develop/Jamfile#L61-L64 because there might still be legitimate uses for it (say if you have multiple `Boost.Python` builds, one against a release- and one against a debug-build of Python). Though the need for that flag is definitely reduced now.

---

## Comment 12

> Username: aminiussi  
> Created at: 2018-05-20 16:42:27 UTC  
> Url: https://github.com/boostorg/python/issues/203#issuecomment-390494987  

Let see if I understand this: we (python related stuff in boost) are moving from a ${major} only library suffix to a ${major}${minor} suffix (which sounds like a good idea) and we need to replicate the work in all python related libs as doing it in the super project would probably break things.   
  
Just one question regarding the Marangozov allocator: it seems that Python is moving to the encoding: ${major}${minor}${allocator} where ${allocator} is **""** for plain `malloc` and **m** for  Marangozov's optimized wrapper (it impact the library's name, which moves from `libpython3.6.(a|so)` to `libpython3.6m.(a|so)` and the include directory (containing `pyconfig..h`) which move from `<pythondist>/include/python3.6` to `<pythondist>/include/python3.6m`.   
  
Some distribution links the malloc only distribution to the Marangozov distribution -- but not all of them.  
And I guess they can co-exist (why did they introduce that naming convention instead of just moving to the Marangozov allocator if it's better is beyond me).  
  
The modification discussed here does not seems to address this issue,  was it considered and dismissed ?  
  
Again, I am not familiar with boost python bindings yet, so maybe this is an trivial issue.  
  
Thanks again for the help.

---

## Comment 13

> Username: stefanseefeld  
> Created at: 2018-05-20 16:53:15 UTC  
> Url: https://github.com/boostorg/python/issues/203#issuecomment-390495667  

On 20.05.2018 12:42, Alain Miniussi wrote:  
>  
> Let see if I understand this: we (python related stuff in boost) are  
> moving from a ${major} only library suffix to a ${major}${minor}  
> suffix (which sounds like a good idea) and we need to replicate the  
> work in all python related libs as doing it in the super project would  
> probably break things.  
>  
  
Not entirely. Before my recent change we used an ad-hoc convention which  
produced `boost_python` (when built with Python 2) and `boost_python3`  
(when built with Python 3). Now we are using `boost_python${major}${minor}`.  
  
> Just one question regarding the Marangozov allocator: it seems that  
> Python is moving to the encoding: ${major}${minor}${allocator} where  
> ${allocator} is *""* for plain |malloc| and *m* for Marangozov's  
> optimized wrapper (it impact the library's name, which moves from  
> |libpython3.6.(a|so)| to |libpython3.6m.(a|so)| and the include  
> directory (containing |pyconfig..h|) which move from  
> |<pythondist>/include/python3.6| to |<pythondist>/include/python3.6m|.  
>  
> Some distribution links the malloc only distribution to the Marangozov  
> distribution -- but not all of them.  
> And I guess they can co-exist (why did they introduce that naming  
> convention instead of just moving to the Marangozov allocator if it's  
> better is beyond me).  
>  
> The modification discussed here does not seems to address this issue,  
> was it considered and dismissed ?  
>  
  
So IIUC, you are proposing that instead of the `${major}${minor}`  
suffix, we use `${major}${minor}${allocator}` ?  
The OP requested that we uniformize the `python-tag` use across Boost  
libraries (notably `Boost.Python` and `Boost.MPI`, which may in fact be  
the only two projects concerned), so what you seem to propose seems to  
be (mostly) unrelated to the issue under consideration here. In the  
interest of keeping the discussion focussed, would you please submit a  
new issue if you are indeed suggesting a suffix change that includes the  
allocator ?  
(To be honest, I'm not convinced of the value of such a change, as I  
have yet to see a platform that has the same Python versions with and  
without that allocator. But OK, let's discuss this elsewhere.)  
  
  
Thanks,  
  
Stefan  
  
--   
  
      ...ich hab' noch einen Koffer in Berlin...

---

## Comment 14

> Username: Lastique  
> Created at: 2018-05-20 23:02:17 UTC  
> Url: https://github.com/boostorg/python/issues/203#issuecomment-390519651  

@stefanseefeld Yes, I'm building Debian packages for my project. The building scripts are based on the official Debian packages and are intended to be compatible. The issue originated from Boost.Python failing to build because its library names have changed compared to 1.66.  
  
Indeed, I'm passing `--python-buildid` when building Boost.Python and Boost.MPI, but I don't think I can remove it because this would break Boost.MPI. Right now I solved the problem locally by reverting Boost.Python to use the global `python-tag`.  
  
Regarding the use of Python, I think you are correct that Boost.Python and Boost.MPI are the only users *within Boost*. I'm not sure if there are Boost.Build-based users of Python *outside Boost*, but I wouldn't deny the possibility. There certainly are Boost.Build-based users of Boost out there.

---

## Comment 15

> Username: stefanseefeld  
> Created at: 2018-05-20 23:09:10 UTC  
> Url: https://github.com/boostorg/python/issues/203#issuecomment-390519994  

On 20.05.2018 19:02, Andrey Semashev wrote:  
>  
> @stefanseefeld <https://github.com/stefanseefeld> Yes, I'm building  
> Debian packages for my project. The building scripts are based on the  
> official Debian packages and are intended to be compatible. The issue  
> originated from Boost.Python failing to build because its library  
> names have changed compared to 1.66.  
>  
  
Can you elaborate on "failing to build" ? I suppose it's your wrapper  
build logic that fails because of it not expecting the new library  
names. Right ?  
  
> Indeed, I'm passing |--python-buildid| when building Boost.Python and  
> Boost.MPI, but I don't think I can remove it because this would break  
> Boost.MPI. Right now I solved the problem locally by reverting  
> Boost.Python to use the global |python-tag|.  
>  
That's sad.  
  
> Regarding the use of Python, I think you are correct that Boost.Python  
> and Boost.MPI are the only users /within Boost/. I'm not sure if there  
> are Boost.Build-based users of Python /outside Boost/, but I wouldn't  
> deny the possibility. There certainly are Boost.Build-based users of  
> Boost out there.  
>  
  
So it seems logical to aim for Boost.MPI also switching to this new  
naming convention. Then everyone could simply adjust their expectations  
and stop using `--python-buildid`.  
But downstream package maintainers reverting my change is rather  
unexpected. I had hoped for a more happy embrace. :-)  
  
  
Stefan  
  
--   
  
      ...ich hab' noch einen Koffer in Berlin...

---

## Comment 16

> Username: Lastique  
> Created at: 2018-05-20 23:20:58 UTC  
> Url: https://github.com/boostorg/python/issues/203#issuecomment-390520657  

> Can you elaborate on "failing to build" ? I suppose it's your wrapper build logic that fails because of it not expecting the new library names. Right ?  
  
Right, exactly. But I don't think the problem is limited to just the packaging. I would expect that the change to also affect any downstream projects that link with Boost.Python - those will need updating, too.

---

## Comment 17

> Username: stefanseefeld  
> Created at: 2018-05-20 23:44:29 UTC  
> Url: https://github.com/boostorg/python/issues/203#issuecomment-390521929  

Yes, of course. That's a feature, not a bug. :-)

---

## Comment 18

> Username: xnox  
> Created at: 2018-05-23 19:51:28 UTC  
> Url: https://github.com/boostorg/python/issues/203#issuecomment-391474782  

@stefanseefeld  
Hi, slightly more involved maintainer of boost in Debian & Ubuntu here.  
  
I'm all for having whichever boost sonames possible, as long as they are consistent and usable by the reverse dependencies. In Debian & Ubuntu, we used to provide boost-python and boost-python3 sonames, which are symlinks to -py27 & py36 buildid tagged things. The current debian packaging has become incompatible with latest boost releases, simply because well the debian packaging has not bee updated it.  
  
The key thing, is in Debian/Ubuntu there is demand for multiple concurrent support of multiple major releases. Currently it's 2.7 & 3.6, soon it will be 2.7/3.6/3.7, and maybe some time later it will become 2.7/3.7. The boost_python3.so symlinks were very convenient as reverse dependencies would link against that to build against the "distro current default python3".  
  
I will double check what is the latest sonames advice is for python related sonames, after taking into account everything that was merged so far. Historically, in Debian there have been requests to provide boost_python builds not only for all the currently supported pythons, but also their debug equivalents. But so far I have not even attempted to make that.  
  
Historically, I also believe Debian started to provide 2 & 3 builds in parallel, and we somewhat had to make up the names ourselves, which is unfortunate. It would be really nice if the upstream boost build system would compile for python2 and python3 simultaneously and in parallel, with whatever non-conflicting sonames you want to call the libraries as long as they are kind of stable and predictable. There are about 73 reverse binary packages dependencies on boost-python in Debian/Ubuntu, 20 of which use python3. It would be nice if everything or most things would switch to python3 by now.  
  
Anyway, time for me to check all the latest greatest things in master & devel branches.

---

## Comment 19

> Username: stefanseefeld  
> Created at: 2018-05-23 20:01:34 UTC  
> Url: https://github.com/boostorg/python/issues/203#issuecomment-391477685  

@xnox, I don't quite understand what you are saying. I'm fully aware that my recent naming changes are not backward compatible. However, I don't see any problem setting up symlinks from `libboost_python27.so` to `libboost_python.so` and from `libboost_python36.so` to `libboost_python3.so`, so "-lboost_python" and "-lboost_python3" would still work for users unable to update their build logic. (And sadly Boost never established any ABI compatibility requirements, so ABI compatibility isn't even on the table.)  
  
In addition, I think an `--python-buildid=d[ebug]` argument could be used to generate a `libboost_python36-d.so` library that links against a debug-enabled Python instance. That's exactly what the `--python-buildid` option was meant to support.

---

## Comment 20

> Username: xnox  
> Created at: 2018-05-23 21:06:25 UTC  
> Url: https://github.com/boostorg/python/issues/203#issuecomment-391496332  

@stefanseefeld ack, I'll check to adjust debian packaging to that effect. I think we have been using buildid argument in lieu of python-tag, and should simply stop doing that in debian/ubuntu.

---

## Comment 21

> Username: xnox  
> Created at: 2018-07-03 15:36:10 UTC  
> Url: https://github.com/boostorg/python/issues/203#issuecomment-402200125  

@stefanseefeld   
I think what I did in https://github.com/boostorg/mpi/pull/58/commits/bd9e3b81a712b9f7e4ecd51cc6de2d7210395568 is still not right =(  
  
As merged by @aminiussi  
  
```  
$ cat user-config-2.7.jam  
using gcc : : : <compileflags>"-Wdate-time -D_FORTIFY_SOURCE=2" <cflags>"-g -O2 -fdebug-prefix-map=/home/xnox/salsa/boost1.67-1.67.0=. -fstack-protector-strong -Wformat -Werror=format-security" <cxxflags>"-g -O2 -fdebug-prefix-map=/home/xnox/salsa/boost1.67-1.67.0=. -fstack-protector-strong -Wformat -Werror=format-security -Wno-unused-local-typedefs" <linkflags>"-Wl,-Bsymbolic-functions -Wl,-z,relro -Wl,-z,now" ;  
using mpi ;  
using python : 2.7 : /usr ;  
```  
  
Similar config is generated for 3.6 and 3.7  
  
When  building above with:  
```  
./bjam -j12 -d2  --layout=system --ignore-site-config --user-config=/home/xnox/salsa/boost1.67-1.67.0/user-config.jam debug-symbols=on --build-dir=build-2.7 --user-config=/home/xnox/salsa/boost1.67-1.67.0/user-config-2.7.jam --with-python --with-mpi python=2.7  
```  
  
The results are a bit unexpected:  
```  
$ /bin/ls stage/lib/ | sort  
libboost_mpi.a  
libboost_mpi_python.a  
libboost_mpi_python.so  
libboost_mpi_python.so.1.67.0  
libboost_mpi.so  
libboost_mpi.so.1.67.0  
libboost_numpy27.a  
libboost_numpy27.so  
libboost_numpy27.so.1.67.0  
libboost_python27.a  
libboost_python27.so  
libboost_python27.so.1.67.0  
libboost_serialization.a  
libboost_serialization.so  
libboost_serialization.so.1.67.0  
mpi.so  
```  
  
I was expecting instead `libboost_mpi_python27.{a,so,so.1.67.0}` to be created note the lack of `27`.  
  
It's as if something is missing from `libs/mpi/build/Jamfile.v2` to make it act like `libs/python/build/Jamfile` is there some trick to force add the `27` "tag" to `boost_mpi_python`?

---

## Comment 22

> Username: xnox  
> Created at: 2018-07-03 15:47:01 UTC  
> Url: https://github.com/boostorg/python/issues/203#issuecomment-402203614  

I think i figured it out.... i've ensure that python-tag things are added in the mpi Jamfile.... but also carbon copy&pasted `https://github.com/boostorg/python/blob/develop/Jamfile` rules into the `libs/mpi/Jamfile.v2` since otherwise it is ineffective, as the `python-tag` rule is not available / not imported.

---

## Comment 23

> Username: stefanseefeld  
> Created at: 2018-07-03 16:01:43 UTC  
> Url: https://github.com/boostorg/python/issues/203#issuecomment-402208381  

Correct ! Make sure your autolink logic is adjusted accordingly.
