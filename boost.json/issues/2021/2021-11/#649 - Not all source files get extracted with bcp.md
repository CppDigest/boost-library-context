# #649 - Not all source files get extracted with bcp [Open]

> Username: tomaszmi  
> Created at: 2021-11-13 09:21:57 UTC  
> Updated at: 2021-11-14 16:13:02 UTC  
> Url: https://github.com/boostorg/json/issues/649  

### Version of Boost: 1.76  
When extracting `Boost.JSON` from the `boost` sources using the `bcp` tool, it seems that not all files get properly copied.   
  
### Steps necessary to reproduce the problem:  
```  
curl --silent --show-error -L https://boostorg.jfrog.io/artifactory/main/release/1.76.0/source/boost_1_76_0.tar.bz2 -o ./boost.tar.bz2  
mkdir boost_build  
tar xf boost.tar.bz2 -C ./boost_build --strip-components=1  
cd boost_build && ./bootstrap.sh && ./b2 tools  
mkdir ~/boost_json_extract/  
./dist/bin/bcp boost/json.hpp ~/boost_json_extract/  
tree ~/boost_json_extract > content_stage1.txt  
./dist/bin/bcp boost/json/src.hpp ~/boost_json_extract/  
tree ~/boost_json_extract > content_stage2.txt  
```  
  
The following are the content files generated.  
[content_stage1.txt](https://github.com/boostorg/json/files/7531724/content_stage1.txt)  
[content_stage2.txt](https://github.com/boostorg/json/files/7531725/content_stage2.txt)  
  
Please note that the `content_stage1.txt` file does not contain the `src.hpp` file - it gets copied only when passing it to `bcp` explicitly (so exists in the `content_stage2.txt` file), however none of these text files contains the `src.cpp` file entry.  
  
### All relevant compiler information  
Apple clang version 13.0.0 (clang-1300.0.29.3), MacBook Pro (13-inch, M1, 2020).  
It seems to be compiler independent.

---

## Comment 1

> Username: grisumbras  
> Created at: 2021-11-13 11:01:54 UTC  
> Url: https://github.com/boostorg/json/issues/649#issuecomment-968050484  

After some investigation I discovered that this is due to optional header-only support (src.cpp does not contain any function definitions, `.ipp` files do). Unfortunately I don't think we can do anything to fix this on our side. Effectively, we'd have to move all `.ipp` files to `src` directory, but that would break `b2 headers`.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-11-14 14:12:33 UTC  
> Url: https://github.com/boostorg/json/issues/649#issuecomment-968298471  

@pdimov ?

---

## Comment 3

> Username: pdimov  
> Created at: 2021-11-14 14:32:24 UTC  
> Url: https://github.com/boostorg/json/issues/649#issuecomment-968301916  

??

---

## Comment 4

> Username: pdimov  
> Created at: 2021-11-14 14:45:55 UTC  
> Url: https://github.com/boostorg/json/issues/649#issuecomment-968304250  

Judging by  
```  
INFO: tracking source dependencies of library container due to presence of "BOOST_CONTAINER_DECL memory_resource* new_delete_resource() BOOST_NOEXCEPT;" in file "boost\container\pmr\global_resource.hpp"  
```  
`bcp` has some heuristic to figure out when a library is compiled, but I don't know what it is.

---

## Comment 5

> Username: grisumbras  
> Created at: 2021-11-14 15:06:16 UTC  
> Url: https://github.com/boostorg/json/issues/649#issuecomment-968307822  

I've already explained the reason above: src.cpp doesn't actually contain any function definitions. I guess, we could put a fake function into the file, but, IMO, that's silly. `bcp json output_dir` should still work.

---

## Comment 6

> Username: grisumbras  
> Created at: 2021-11-14 15:08:02 UTC  
> Updated at: 2021-11-14 15:10:43 UTC  
> Url: https://github.com/boostorg/json/issues/649#issuecomment-968308185  

Also, I suspect making bcp copy sources won't matter much, because building Json also depends on Config jam files, which aren't copied. At this point, it's simpler for users to run `bcp json/src.hpp` and use Json as a header-only library.

---

## Comment 7

> Username: pdimov  
> Created at: 2021-11-14 15:32:21 UTC  
> Url: https://github.com/boostorg/json/issues/649#issuecomment-968313249  

> I've already explained the reason above: src.cpp doesn't actually contain any function definitions.  
  
That's not what the word "explained" means.  
  
> I guess, we could put a fake function into the file, but, IMO, that's silly.  
  
I don't see anything particularly wrong with that. The other option is fixing this on the `bcp` side, but I'm not sure how.

---

## Comment 8

> Username: pdimov  
> Created at: 2021-11-14 15:38:48 UTC  
> Url: https://github.com/boostorg/json/issues/649#issuecomment-968314341  

Oh, `bcp` supports giving it a name of a source file. So `dist\bin\bcp libs/json/src/src.cpp tmp-json\` works, kind of. It doesn't copy the `Jamfile` but it's not clear whether people who use `bcp` need it.

---

## Comment 9

> Username: grisumbras  
> Created at: 2021-11-14 15:45:15 UTC  
> Url: https://github.com/boostorg/json/issues/649#issuecomment-968315417  

There's essentially no point in copying src.cpp without copying build scripts.

---

## Comment 10

> Username: pdimov  
> Created at: 2021-11-14 15:50:00 UTC  
> Url: https://github.com/boostorg/json/issues/649#issuecomment-968316136  

`dist\bin\bcp libs/json/src libs/json/build tmp-json\` then.

---

## Comment 11

> Username: grisumbras  
> Created at: 2021-11-14 15:52:52 UTC  
> Url: https://github.com/boostorg/json/issues/649#issuecomment-968316601  

I'm not at my computer, but IIRC, you also need at least boost-install and most of Config (not just the headers)

---

## Comment 12

> Username: pdimov  
> Created at: 2021-11-14 16:13:02 UTC  
> Url: https://github.com/boostorg/json/issues/649#issuecomment-968320096  

`bcp` has logic to copy the necessary files, which I've updated in https://github.com/boostorg/bcp/pull/5 to include `tools/boost-install` and `libs/headers`, but I don't know why it doesn't activate here.
