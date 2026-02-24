# #63 - Header location [Closed]

> Username: pdimov  
> Created at: 2017-10-14 16:02:28 UTC  
> Updated at: 2017-10-18 22:36:50 UTC  
> Closed at: 2017-10-18 22:36:50 UTC  
> Url: https://github.com/boostorg/winapi/issues/63  

It's very unfortunate that Winapi places all of its headers into `boost/detail` instead of `boost/winapi/...`. This irregularity means that every header is an exception for the purposes of determining dependencies.

---

## Comment 1

> Username: Lastique  
> Created at: 2017-10-14 20:10:13 UTC  
> Url: https://github.com/boostorg/winapi/issues/63#issuecomment-336663602  

Are you proposing to move the headers to `boost/winapi`?  
  
Historically, WinAPI headers were initially part of Boost.Detail. I'm not even sure we intended to have it as a separate library at the very start. And although now it is a library, it's still not for public use, hence the `detail`.  
  
In general, I'm not opposed to moving the headers if it helps (not sure how, though), but I would still prefer to keep Boost.WinAPI our private tool.

---

## Comment 2

> Username: pdimov  
> Created at: 2017-10-14 21:31:03 UTC  
> Url: https://github.com/boostorg/winapi/issues/63#issuecomment-336668859  

The way it helps is that with regular includes, it's possible to determine from `#include <boost/foo/...>` that there is a dependency on the `foo` library without checking out the whole Boost and scanning it.  
  
I don't argue against keeping Winapi our private tool here (even though I think that it would be as useful for the general public as it is for us, in its present form.)

---

## Comment 3

> Username: viboes  
> Created at: 2017-10-14 23:46:23 UTC  
> Url: https://github.com/boostorg/winapi/issues/63#issuecomment-336675366  

@Lastique Even if we locate it inside the detail folder/namespace, I believe this doesn't prevent people from using it.  
  
@pdimov Did you mean that your dependency tool doesn't identifies winapi as a separated library?

---

## Comment 4

> Username: Lastique  
> Created at: 2017-10-15 16:22:33 UTC  
> Url: https://github.com/boostorg/winapi/issues/63#issuecomment-336722803  

> even though I think that it would be as useful for the general public as it is for us, in its present form.  
  
We're using Boost.WinAPI because we want to use Windows stuff in headers without conflicts. Users can simply include `windows.h` in their code.  
  
> Even if we locate it inside the detail folder/namespace, I believe this doesn't prevent people from using it.  
  
Sure. As putting stuff in a `detail` namespace doesn't. The point is that stuff in the `detail` folder or namespace is obviously not for public use and we are not bound to preserve any kind of compatibility about it. Stuff in the top level `boost` directory is interpreted as all public by default.  
  
> Did you mean that your dependency tool doesn't identifies winapi as a separated library?  
  
I think the tool is able to track dependencies as it is. The rationale is that humans typically expect all the stuff in `boost/detail` belongs to Boost.Detail. I have to say though that we have lots of examples where this expectation is false. Also, `b2 headers` has more work to do as it has to create links to each individual file in `boost/detail` instead of just linking the directory.

---

## Comment 5

> Username: pdimov  
> Created at: 2017-10-15 16:47:29 UTC  
> Url: https://github.com/boostorg/winapi/issues/63#issuecomment-336724470  

Tracking irregular dependencies is only possible with a full Boost checkout, as you have to scan all the repos to build a header -> module map.  
  
A tool whose purpose is to install the necessary Boost subset needs to be able to determine the module without having it already checked out (because if it were checked out, what would be the point of using the tool to install it.)  
  
https://github.com/boostorg/boostdep/blob/develop/depinst/depinst.py  
  
`depinst` looks at `#include <boost/pumpkin/something.hpp>` and assumes this comes from `libs/pumpkin`. Headers that do not follow this convention have to be explicitly listed as exceptions in  
  
https://github.com/boostorg/boostdep/blob/develop/depinst/exceptions.txt  
  
and, even though it won't be possible to get rid of that file entirely for legacy reasons, it would be nice if new headers do not need to be listed so that I don't have to change `exceptions.txt` each time someone makes a change somewhere.

---

## Comment 6

> Username: Lastique  
> Created at: 2017-10-15 16:50:45 UTC  
> Url: https://github.com/boostorg/winapi/issues/63#issuecomment-336724698  

Would it be better if the dependencies are generated once and saved in a file in the `meta` directory? I think, inferring dependencies from the directory structure is rather fragile.

---

## Comment 7

> Username: Lastique  
> Created at: 2017-10-15 16:56:07 UTC  
> Url: https://github.com/boostorg/winapi/issues/63#issuecomment-336725053  

BTW, the lists below `winapi` and `stacktrace` in the `exceptions.txt` file are outdated. I also removed `boost/utility/empty_deleter.hpp`.

---

## Comment 8

> Username: pdimov  
> Created at: 2017-10-15 17:00:04 UTC  
> Url: https://github.com/boostorg/winapi/issues/63#issuecomment-336725312  

Inferring the dependencies has the advantage that the maintainers don't need to do any work to keep the dependency list up to date.  
  
Yes, I know that the list is outdated. I was going to update it when I noticed that the list of Winapi headers has changed.

---

## Comment 9

> Username: Lastique  
> Created at: 2017-10-15 17:14:26 UTC  
> Updated at: 2017-10-15 17:14:48 UTC  
> Url: https://github.com/boostorg/winapi/issues/63#issuecomment-336726246  

Ok, I will do the move sometime next week. I'll have to create PRs for the dependent libraries as well.

---

## Comment 10

> Username: pdimov  
> Created at: 2017-10-15 17:17:40 UTC  
> Url: https://github.com/boostorg/winapi/issues/63#issuecomment-336726435  

Thanks!

---

## Comment 11

> Username: viboes  
> Created at: 2017-10-15 23:27:23 UTC  
> Url: https://github.com/boostorg/winapi/issues/63#issuecomment-336749860  

Hi Peter. I can get rid of   
```  
thread:  
  boost/detail/atomic_redef_macros.hpp  
boost/detail/atomic_undef_macros.hpp  
```

---

## Comment 12

> Username: viboes  
> Created at: 2017-10-15 23:31:25 UTC  
> Url: https://github.com/boostorg/winapi/issues/63#issuecomment-336750064  

Even if there is no archive library, what would be the impact of extracting anything in boost/archive from the serialization library?

---

## Comment 13

> Username: pdimov  
> Created at: 2017-10-16 00:06:46 UTC  
> Url: https://github.com/boostorg/winapi/issues/63#issuecomment-336752131  

I don't mind that much the existing exceptions, as long as we don't keep adding new ones. :-)  
  
Extracting the archive part of the Serialization library into its own Archive module would be an interesting refactoring exercise in principle. This would cut dependencies since many libraries use only the Serialization part (to support serialization for their types), but not the Archive part. But last time I looked it seemed nontrivial, and I doubt that Robert would be enthusiastic about such a project.

---

## Comment 14

> Username: viboes  
> Created at: 2017-10-16 19:05:54 UTC  
> Url: https://github.com/boostorg/winapi/issues/63#issuecomment-336999042  

I know that Robert is not for this split.   
  
What would be the result without changing anything in Boost.Serialization, but just changing your tool. Which applications would we break?  
  
At the end, we could require the user of your tool to use archive when they want everything. Would this be plausible?

---

## Comment 15

> Username: Lastique  
> Created at: 2017-10-18 18:17:43 UTC  
> Url: https://github.com/boostorg/winapi/issues/63#issuecomment-337681811  

@pdimov Would you like me to move `interlocked.hpp` somewhere else as well?

---

## Comment 16

> Username: pdimov  
> Created at: 2017-10-18 19:15:15 UTC  
> Url: https://github.com/boostorg/winapi/issues/63#issuecomment-337698600  

No strong preference either way.

---

## Comment 17

> Username: Lastique  
> Created at: 2017-10-18 22:36:50 UTC  
> Url: https://github.com/boostorg/winapi/issues/63#issuecomment-337747839  

Done in https://github.com/boostorg/winapi/commit/6b67a9c5cf88fbb648cdc305d27a32dda4f9bebf.
