# #112 - Name clash with multiple python impls [Open]

> Username: SoapGentoo  
> Created at: 2019-12-22 15:31:26 UTC  
> Updated at: 2020-06-22 14:02:08 UTC  
> Url: https://github.com/boostorg/mpi/issues/112  

On Gentoo, we usually build Boost with multiple python impls. Since 1.72 we are getting name clashes when building with MPI and multiple python impls:  
```  
error: Name clash for '<p/var/tmp/portage/dev-libs/boost-1.72.0/work/boost_1_72_0-abi_x86_64.amd64/stage/lib>mpi.so'  
error:   
error: Tried to build the target twice, with property sets having   
error: these incompatible properties:  
error:   
error:     -  <include>/usr/include/python2.7 <python>2.7 <xdll-path>/var/tmp/portage/dev-libs/boost-1.72.0/work/boost_1_72_0-abi_x86_64.amd64/bin.v2/libs/mpi/build/gcc-9.2/gentoorelease/local-visibility-global/pch-off/python-2.7/threading-multi/visibility-hidden <xdll-path>/var/tmp/portage/dev-libs/boost-1.72.0/work/boost_1_72_0-abi_x86_64.amd64/bin.v2/libs/python/build/gcc-9.2/gentoorelease/pch-off/python-2.7/threading-multi/visibility-hidden  
error:     -  <include>/usr/include/python3.6m <python>3.6 <xdll-path>/var/tmp/portage/dev-libs/boost-1.72.0/work/boost_1_72_0-abi_x86_64.amd64/bin.v2/libs/mpi/build/gcc-9.2/gentoorelease/local-visibility-global/pch-off/python-3.6/threading-multi/visibility-hidden <xdll-path>/var/tmp/portage/dev-libs/boost-1.72.0/work/boost_1_72_0-abi_x86_64.amd64/bin.v2/libs/python/build/gcc-9.2/gentoorelease/pch-off/python-3.6/threading-multi/visibility-hidden  
```  
this is very likely due to the change in https://github.com/boostorg/mpi/commit/3ecbf83ff33750ceffda199de97283d200357633. @Lastique @pdimov any ideas on how we could fix this? I tried injecting all sorts of tags into the Jamfile, but to no avail, it always crashes with the name clash error above. Our bug report: https://bugs.gentoo.org/703036 building it against a single python impl solves the issue, but isn't a tractable solution for gentoo users.

---

## Comment 1

> Username: Lastique  
> Created at: 2019-12-22 17:10:00 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568283102  

As a workaround, you could invoke `b2` separately for every Python version you require.  
  
As to why `b2` fails in a single run, I'm not sure. Python version already seems to be part of the target path, so I don't understand the problem.

---

## Comment 2

> Username: SoapGentoo  
> Created at: 2019-12-22 17:16:33 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568283835  

While that's a possibility, it's prohibitively expensive if you have 5 python impls enabled. @stefanseefeld seems to suggest that Boost.MPI should do the same thing in https://github.com/boostorg/python/issues/203#issuecomment-388963741. I've tried a lot, but have failed to get any workable solution that tags all Boost.MPI libs in a similar fashion to Boost.Python.

---

## Comment 3

> Username: Lastique  
> Created at: 2019-12-22 17:28:10 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568284792  

> While that's a possibility, it's prohibitively expensive if you have 5 python impls enabled.  
  
Why? You don't have to build the whole Boost for each Python, just Boost.Python and Boost.MPI, and you have to do this in a single run anyway.

---

## Comment 4

> Username: pdimov  
> Created at: 2019-12-22 17:32:26 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568285217  

Building with both 2 and 3 in a single run was certainly intended to be supported, and breaking it is no good.

---

## Comment 5

> Username: pdimov  
> Created at: 2019-12-22 17:41:12 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568286296  

As far as I can see, fixing this should be as easy as copying these two lines:  
  
https://github.com/boostorg/mpi/blob/develop/build/Jamfile.v2#L136-L137  
  
to the corresponding place at  
  
https://github.com/boostorg/mpi/blob/develop/build/Jamfile.v2#L171  
  
and then adding `PYTHON_EXTENSION` to the list `STATIC_LIB SHARED_LIB IMPORT_LIB` at  
  
https://github.com/boostorg/mpi/blob/develop/build/Jamfile.v2#L61  
  
and  
  
https://github.com/boostorg/mpi/blob/develop/build/Jamfile.v2#L67  
  
But I can't test this at the moment because I've no idea how to set up MPI under Windows and have no time to figure it out.  
  
Let me know if this doesn't work and I'll try to look into it more deeply.

---

## Comment 6

> Username: stefanseefeld  
> Created at: 2019-12-22 17:57:01 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568288375  

Please consider cloning the `Boost.Python` re-implementation of the `python-tag` function (https://github.com/boostorg/python/blob/develop/Jamfile#L52-L68) to add a Python-version-specific suffix to the respective libraries.  
Once that works correctly for `Boost.MPI`, we can fix the upstream version of that function (https://github.com/boostorg/boost/blob/master/boostcpp.jam#L222-L250) and get rid of the overrides in both `Boost.Python` as well as `Boost.MPI`.

---

## Comment 7

> Username: pdimov  
> Created at: 2019-12-22 18:12:29 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568289540  

It's already cloned. https://github.com/boostorg/mpi/blob/develop/build/Jamfile.v2#L58-L74

---

## Comment 8

> Username: pdimov  
> Created at: 2019-12-22 18:13:40 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568289622  

In addition, the upstream version seems to do the same thing as these local overrides. What specifically do you want fixed?

---

## Comment 9

> Username: SoapGentoo  
> Created at: 2019-12-22 18:30:36 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568290883  

This is my current patch:  
```  
--- a/libs/mpi/build/Jamfile.v2  
+++ b/libs/mpi/build/Jamfile.v2  
@@ -58,13 +58,13 @@  
 rule python-tag ( name : type ? : property-set )  
 {  
     local result = $(name) ;  
-    if $(type) in STATIC_LIB SHARED_LIB IMPORT_LIB  
+    if $(type) in STATIC_LIB SHARED_LIB IMPORT_LIB python-extension  
     {  
         local version = [ $(property-set).get <python> ] ;  
         local lib-suffix = [ version-suffix $(version) ] ;  
         result = $(result)$(lib-suffix) ;  
     }  
-    if $(type) in STATIC_LIB SHARED_LIB IMPORT_LIB && $(PYTHON_ID)  
+    if $(type) in STATIC_LIB SHARED_LIB IMPORT_LIB python-extension && $(PYTHON_ID)  
     {  
         result = $(result)-$(PYTHON_ID) ;  
     }  
@@ -168,6 +168,8 @@  
                 <link>shared:<define>BOOST_PYTHON_DYN_LINK=1  
                 <link>shared <runtime-link>shared  
                 <python-debugging>on:<define>BOOST_DEBUG_PYTHON  
+                -<tag>@$(BOOST_JAMROOT_MODULE)%$(BOOST_JAMROOT_MODULE).tag  
+                <tag>@$(__name__).python-tag  
               ;  
   
             mpi_python_libs = boost_mpi_python mpi ;  
```  
and it doesn't solve the issue. My gut feeling is that it's related to the `python-extension` not getting the tag.

---

## Comment 10

> Username: SoapGentoo  
> Created at: 2019-12-22 18:35:47 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568291276  

I have removed the `if $(type) in STATIC_LIB SHARED_LIB IMPORT_LIB` conditional, thereby applying the python-tag modifications unconditionally, and still get the unmodified `mpi.so` target.

---

## Comment 11

> Username: pdimov  
> Created at: 2019-12-22 18:42:26 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568291818  

Should be PYTHON_EXTENSION, not python-extension. Although if with the `if` removed it still doesn't apply, that's not the problem.

---

## Comment 12

> Username: SoapGentoo  
> Created at: 2019-12-22 18:43:24 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568291947  

@pdimov before trying `python-extension`, I tried `PYTHON_EXTENSION` too, still fails.

---

## Comment 13

> Username: pdimov  
> Created at: 2019-12-22 19:25:48 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568295945  

@SoapGentoo You're right, the superproject definition of `tag` needs `PYTHON_EXTENSION` added too for this to work.

---

## Comment 14

> Username: pdimov  
> Created at: 2019-12-22 19:36:08 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568296928  

Unrelated to this build problem, the addition of `mpi.so` to `boost-install` is questionable for other reasons; Boost should in all probability not be installing a file named `mpi.so` to `/usr/lib` (which is the default for `b2 install`.) I suspect that we just don't want `mpi` in the `boost-install` line. (And if we insist on installing it, it should be prefixed with `boost_` like everything else, and named something like `boost_mpi_python_extension.so`.)

---

## Comment 15

> Username: pdimov  
> Created at: 2019-12-22 19:50:06 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568297885  

I can fix this build issue by adding PYTHON_EXTENSION here:  
  
https://github.com/boostorg/boost/blob/master/boostcpp.jam#L188  
  
and perhaps here for consistency:  
  
https://github.com/boostorg/boost/blob/master/boostcpp.jam#L226  
  
However, this will likely break Boost.Python's examples such as this one:  
  
https://github.com/boostorg/python/blob/develop/example/quickstart/Jamfile#L15  
  
which at the moment assume an unadorned name for the extension. If I make the change in `boostcpp.jam`, this example will start building something like `extending-mt-gd-x64-1_73.pyd` (or whatever the exact name will turn out to be) on Windows, and will in all probability fail to load it from Python.  
  
So I can't go ahead with the change before all "Python stakeholders" (so to speak) weigh in and tell me what the behavior when building a Python extension .dll/.so ought to be when --layout is `tagged` or `versioned`.  
  
(The example as-is will already acquire a version suffix (`extending27.so`) if built with `python=2.7`, and I'm not sure how it tolerates that.)

---

## Comment 16

> Username: SoapGentoo  
> Created at: 2019-12-22 19:52:32 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568298016  

@pdimov there's a simple solution here imo. Can you just create `<stage_dir>/pythonX.Y/mpi.so` that way, the per-impl `mpi.so` are correctly namespaced, and we can install them in the per-impl `site-packages`, yet don't have to break all examples. Do you have a patch for me to try? I honestly don't understand where to put all these `python-tag` rules.

---

## Comment 17

> Username: stefanseefeld  
> Created at: 2019-12-22 19:54:11 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568298125  

You raise a good point: Python extensions shouldn't contain the python version they were compiled for in their name. Rather, their build directory should be used to disambiguate the variant (just as Boost.Build handles build variants everywhere else.  
Then during installation, the install prefix of course needs to be queried from the Python version that's being targeted. Thus, `mpi.so` shouldn't end up in `/usr/lib`, but rather in something like `/usr/lib/python<py-version>/site-packages` or somesuch.

---

## Comment 18

> Username: stefanseefeld  
> Created at: 2019-12-22 19:54:42 UTC  
> Updated at: 2019-12-22 19:55:47 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568298158  

@pdimov , hah, I think we just said the very same thing. :-)  
Sorry, I misread. It's @SoapGentoo I'm in agreement with.

---

## Comment 19

> Username: SoapGentoo  
> Created at: 2019-12-22 19:55:50 UTC  
> Updated at: 2019-12-22 19:56:12 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568298249  

@stefanseefeld correct, even though I'd suggest letting us handle installing into `/usr/lib/python<py-version>/site-packages` (which is trivial for us in Gentoo, but is tricky for b2, as Gentoo uses `/usr/lib64` for py2.7, py3.6 but `/usr/lib` for py3.7+) before fixing the issue here.

---

## Comment 20

> Username: stefanseefeld  
> Created at: 2019-12-22 19:58:32 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568298441  

@SoapGentoo OK, fair enough. For my own builds I'm also mostly avoiding `bjam`. We should establish where things should be installed, independent of what tools are used, then let tools maintainers do whatever is necessary to meet those specs.   
(I hear some people want to move to `CMake`, *gasp*)

---

## Comment 21

> Username: pdimov  
> Created at: 2019-12-22 20:04:56 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568298866  

If `mpi.so` is indeed the correct name for the extension, the immediate fix here is to remove `mpi` from https://github.com/boostorg/mpi/blob/develop/build/Jamfile.v2#L173:  
```  
mpi_python_libs = boost_mpi_python ;  
```  
(The space before the semicolon is significant.)  
  
Installing into `/usr/lib/python<py-version>/site-packages` is a decidedly nontrivial change.

---

## Comment 22

> Username: stefanseefeld  
> Created at: 2019-12-22 20:15:13 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568299594  

@pdimov, I'm not sure how many Python extensions `Boost` contains, and how many of those are actually installed. I expect there are very few, so the change, albeit non-trivial, may actually be manageable. (`Boost.Python` itself for example doesn't provide any installable Python extensions. The only ones it builds are for testing, as well as code snippets for documentation.)

---

## Comment 23

> Username: pdimov  
> Created at: 2019-12-22 20:21:38 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568300047  

Zero, not counting this one, which only became installable in 1.72 due to Andrey's change. This was actually reported to me as a bug in the 1.72 CMake config file installation, but I didn't realize at the time that we simply shouldn't be installing mpi.so at all, not just not generate a CMake config for it.

---

## Comment 24

> Username: Lastique  
> Created at: 2019-12-22 20:24:36 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568300235  

Installing `mpi.so` is important e.g. for Debian packaging. If the library is not installed, it cannot be picked up by the packaging scripts. In Debian case, it is the packaging scripts who ensure the libraries are unpacked in the right place after installation. Putting it in the right place on `b2` stage would be great, but not installing it at all means downstream packagers cannot install it at all.

---

## Comment 25

> Username: SoapGentoo  
> Created at: 2019-12-22 20:25:17 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568300278  

@pdimov not installing it is fine too, as long as it stores it in per-impl dirs in the build directory, we can take it from there. It looks like your `PYTHON_EXTENSION` tricks seem to be working.

---

## Comment 26

> Username: stefanseefeld  
> Created at: 2019-12-22 20:25:53 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568300319  

OK. So I suppose the correct fix is to inform `bjam` as well as `CMake` that `mpi.so` is a Python extension rather than a library, so the appropriate logic is used to determine the (Python-version-specific) install prefix.

---

## Comment 27

> Username: SoapGentoo  
> Created at: 2019-12-22 20:27:16 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568300431  

@stefanseefeld one of my big worries with CMake is that currently, CMake is mentally stuck in the one-py2-one-py3-impl mindset, which makes multi-impl configs like Gentoo's extremely painful (I currently have python3.6, 3.7 and 3.8 installed and working with boost 1.71)

---

## Comment 28

> Username: Lastique  
> Created at: 2019-12-22 20:30:40 UTC  
> Updated at: 2019-12-22 20:32:02 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568300676  

> Zero, not counting this one, which only became installable in 1.72 due to Andrey's change.  
  
It was being installed before, then it broke at some point and was fixed by my PR.

---

## Comment 29

> Username: SoapGentoo  
> Created at: 2019-12-22 20:32:33 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568300818  

@Lastique I'm still pretty sure that compiled python modules shouldn't go into raw `/usr/lib*/`, which is what is happening currently. The proper solution is as @stefanseefeld mentioned to introspect the site-packages dir from python itself, and use that. I'm just worried a bit that it's adding more complexity to b2/boost.build.

---

## Comment 30

> Username: Lastique  
> Created at: 2019-12-22 20:34:55 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568300979  

> I'm still pretty sure that compiled python modules shouldn't go into raw /usr/lib*/, which is what is happening currently.  
  
I'm not arguing with that. I'm just saying that not installing the module is not the solution, it's a regression.

---

## Comment 31

> Username: stefanseefeld  
> Created at: 2019-12-22 20:36:44 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568301083  

@SoapGentoo , I'm using CMake for work (I wish I wouldn't have to...), and I'm determining the Python extension install path as I described. However, I'm not building against multiple Python versions in a single run (that "feature" was introduced by `bjam`, and I still don't like it as it introduces much more complexity than benefit), so I can't comment on how hard it would be to implement that with `CMake`. Adding support for this with `bjam` shouldn't be too hard.

---

## Comment 32

> Username: pdimov  
> Created at: 2019-12-22 22:46:33 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568309489  

I'm testing a fix for the issue on the `boost-install` side: https://github.com/boostorg/boost_install/commit/2be81de4393483cc3b8e0a2e35e4839fa7d04072. This isn't a particularly elegant solution - it would be better to add an `<install-subdir>` feature to `stage.install` and use that - but I'm not qualified for that and @swatanabe is apparently busy with other things, so this (if it works) will have to do for now.

---

## Comment 33

> Username: stefanseefeld  
> Created at: 2019-12-22 22:53:09 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568309841  

I don't understand the Boost.Build internals well enough to comment, but would like to suggest that you capture the essence of this discussion with the appropriate PR (or issue). There is a semantic distinction to be drawn between "libraries" and "python extensions", so ideally some form of polymorphic install behaviour should be invoked (whether this involves installing into a subdir or not).

---

## Comment 34

> Username: SoapGentoo  
> Created at: 2019-12-22 23:15:48 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568311291  

@pdimov Thanks a lot for your efforts. With the patch from the highlighted commit and some creative build script hackery, I now have the following install tree:  
```  
/  
└── usr  
    ├── include  
    │   └── boost  
[...]  
    ├── lib  
    │   ├── python3.7  
    │   │   └── site-packages  
    │   │       └── boost  
    │   │           ├── __init__.py  
    │   │           ├── mpi37.so  
    │   │           ├── mpi.so -> mpi37.so  
    │   │           └── __pycache__  
    │   │               ├── __init__.cpython-37.opt-1.pyc  
    │   │               ├── __init__.cpython-37.opt-2.pyc  
    │   │               └── __init__.cpython-37.pyc  
    │   └── python3.8  
    │       └── site-packages  
    │           └── boost  
    │               ├── __init__.py  
    │               ├── mpi38.so  
    │               ├── mpi.so -> mpi38.so  
    │               └── __pycache__  
    │                   ├── __init__.cpython-38.opt-1.pyc  
    │                   ├── __init__.cpython-38.opt-2.pyc  
    │                   └── __init__.cpython-38.pyc  
    └── lib64  
[...]  
        ├── libboost_mpi-mt.so -> libboost_mpi.so  
        ├── libboost_mpi_python27-mt.so -> libboost_mpi_python27.so  
        ├── libboost_mpi_python27.so -> libboost_mpi_python27.so.1.72.0  
        ├── libboost_mpi_python27.so.1 -> libboost_mpi_python27.so.1.72.0  
        ├── libboost_mpi_python27.so.1.72 -> libboost_mpi_python27.so.1.72.0  
        ├── libboost_mpi_python27.so.1.72.0  
        ├── libboost_mpi_python36-mt.so -> libboost_mpi_python36.so  
        ├── libboost_mpi_python36.so -> libboost_mpi_python36.so.1.72.0  
        ├── libboost_mpi_python36.so.1 -> libboost_mpi_python36.so.1.72.0  
        ├── libboost_mpi_python36.so.1.72 -> libboost_mpi_python36.so.1.72.0  
        ├── libboost_mpi_python36.so.1.72.0  
        ├── libboost_mpi_python37-mt.so -> libboost_mpi_python37.so  
        ├── libboost_mpi_python37.so -> libboost_mpi_python37.so.1.72.0  
        ├── libboost_mpi_python37.so.1 -> libboost_mpi_python37.so.1.72.0  
        ├── libboost_mpi_python37.so.1.72 -> libboost_mpi_python37.so.1.72.0  
        ├── libboost_mpi_python37.so.1.72.0  
        ├── libboost_mpi_python38-mt.so -> libboost_mpi_python38.so  
        ├── libboost_mpi_python38.so -> libboost_mpi_python38.so.1.72.0  
        ├── libboost_mpi_python38.so.1 -> libboost_mpi_python38.so.1.72.0  
        ├── libboost_mpi_python38.so.1.72 -> libboost_mpi_python38.so.1.72.0  
        ├── libboost_mpi_python38.so.1.72.0  
        ├── libboost_mpi.so -> libboost_mpi.so.1.72.0  
        ├── libboost_mpi.so.1 -> libboost_mpi.so.1.72.0  
        ├── libboost_mpi.so.1.72 -> libboost_mpi.so.1.72.0  
        ├── libboost_mpi.so.1.72.0  
[...]  
        ├── libboost_python27-mt.so -> libboost_python27.so  
        ├── libboost_python27.so -> libboost_python27.so.1.72.0  
        ├── libboost_python27.so.1 -> libboost_python27.so.1.72.0  
        ├── libboost_python27.so.1.72 -> libboost_python27.so.1.72.0  
        ├── libboost_python27.so.1.72.0  
        ├── libboost_python36-mt.so -> libboost_python36.so  
        ├── libboost_python36.so -> libboost_python36.so.1.72.0  
        ├── libboost_python36.so.1 -> libboost_python36.so.1.72.0  
        ├── libboost_python36.so.1.72 -> libboost_python36.so.1.72.0  
        ├── libboost_python36.so.1.72.0  
        ├── libboost_python37-mt.so -> libboost_python37.so  
        ├── libboost_python37.so -> libboost_python37.so.1.72.0  
        ├── libboost_python37.so.1 -> libboost_python37.so.1.72.0  
        ├── libboost_python37.so.1.72 -> libboost_python37.so.1.72.0  
        ├── libboost_python37.so.1.72.0  
        ├── libboost_python38-mt.so -> libboost_python38.so  
        ├── libboost_python38.so -> libboost_python38.so.1.72.0  
        ├── libboost_python38.so.1 -> libboost_python38.so.1.72.0  
        ├── libboost_python38.so.1.72 -> libboost_python38.so.1.72.0  
        ├── libboost_python38.so.1.72.0  
[...]  
        ├── python2.7  
        │   └── site-packages  
        │       └── boost  
        │           ├── __init__.py  
        │           ├── __init__.pyc  
        │           ├── __init__.pyo  
        │           ├── mpi27.so  
        │           └── mpi.so -> mpi27.so  
        └── python3.6  
            └── site-packages  
                └── boost  
                    ├── __init__.py  
                    ├── mpi36.so  
                    ├── mpi.so -> mpi36.so  
                    └── __pycache__  
                        ├── __init__.cpython-36.opt-1.pyc  
                        ├── __init__.cpython-36.opt-2.pyc  
                        └── __init__.cpython-36.pyc  
```  
which works perfectly with the examples from https://www.boost.org/doc/libs/1_72_0/doc/html/mpi/python.html in all supported python versions. Should I submit the patch that I have right now for inclusion in Boost.MPI?

---

## Comment 35

> Username: pdimov  
> Created at: 2019-12-22 23:45:56 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568313414  

With the boost-install patch, we should no longer need any changes to Boost.MPI. You would just need to copy `mpi.so` from `$LIBDIR/boost-py2.7` or `$LIBDIR/boost-py3.6` to the appropriate `site-packages` directory. The version suffix is no longer necessary.

---

## Comment 36

> Username: SoapGentoo  
> Created at: 2019-12-23 08:53:55 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568407379  

@pdimov maybe we could use this opportunity to centralize the `python-tag` handling stuff and remove the code duplication from Boost.MPI and Boost.Python as suggested by @stefanseefeld ?

---

## Comment 37

> Username: pdimov  
> Created at: 2019-12-23 14:50:04 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-568495202  

Sure, it's possible; as I said, the global `python-tag` rule already does the same as these local ones, so one might just delete the local `python-tag` with its corresponding implementation details and use `<tag>@python-tag` instead of `<tag>@$(__name__).python-tag`. I think I tried that here and it worked, but I'm not 100% certain. If you test that and it works for you, you could make a pull request.

---

## Comment 38

> Username: aminiussi  
> Created at: 2020-06-22 11:52:21 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-647468321  

Hi,  
  
I'he never been able to understand much to bjam, but the current localization of mpi.so is indeed an issue as it seems to prevent the mpi module to be inside the boost package.   
  
What is the status of this issue, is there anything that can be done ?  
  
Thanks

---

## Comment 39

> Username: SoapGentoo  
> Created at: 2020-06-22 11:54:46 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-647469345  

Currently, `mpi.so` works fine in our multi-python installation, it just requires per-impl installation (and some further hackery to nest it in a dedicated dir within site-packages).

---

## Comment 40

> Username: aminiussi  
> Created at: 2020-06-22 12:09:19 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-647476490  

Just to be clear, right now, it's installed in `<dist>/lib/boost-pythonX.Y/mpi.so`, and not in `<dist>/lib/boost-pythonX.Y/boost/mpi.so`. Is that the intended place ?   
  
Thanks

---

## Comment 41

> Username: SoapGentoo  
> Created at: 2020-06-22 13:33:17 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-647522370  

no, it needs to be installed into a python-impl's specific `site-packages` dir, which we omitted in order not to complicate getting this off the ground. In Gentoo, we manually move the `.so` and `__init__.py` into place manually.

---

## Comment 42

> Username: aminiussi  
> Created at: 2020-06-22 14:02:08 UTC  
> Url: https://github.com/boostorg/mpi/issues/112#issuecomment-647539241  

Ok, thanks
