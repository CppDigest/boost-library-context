# #585 - Two broken extra entries for geometry in Boost online documentation (since 1.70) [Closed]

> Username: tinko92  
> Created at: 2019-04-12 12:59:05 UTC  
> Updated at: 2020-04-19 14:36:58 UTC  
> Closed at: 2020-04-19 14:36:58 UTC  
> Url: https://github.com/boostorg/geometry/issues/585  

I noticed (with the release of 1.70) that two extra entries showed up in the online Boost HTML documentation, titled "Geometry Extensions" and "Geometry Index" that share the description with the "Geometry" entry, however, the links lead to 404 errors:  
  
New entries:  
https://www.boost.org/doc/libs/1_70_0/#lib-geometry-extensions  
https://www.boost.org/doc/libs/1_70_0/#lib-geometry-index  
  
Targets with 404 errors:  
https://www.boost.org/doc/libs/1_70_0/libs/geometry/extensions/doc/html/index.html  
https://www.boost.org/doc/libs/1_70_0/libs/geometry/index/doc/html/index.html

---

## Comment 1

> Username: awulkiew  
> Created at: 2019-04-12 13:04:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/585#issuecomment-482566445  

Right, I feared that something like this would happen. It was caused by solution to this issue: https://github.com/boostorg/geometry/issues/575  
and addition of metadata for "sublibs" in order to silence the regression testing suite.

---

## Comment 2

> Username: awulkiew  
> Created at: 2019-04-12 13:05:24 UTC  
> Updated at: 2019-04-12 13:28:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/585#issuecomment-482566882  

@mloskot In case you wanted to do the same for GIL. This is the result.  
  
EDIT: If you do this wrong. :)

---

## Comment 3

> Username: awulkiew  
> Created at: 2019-04-12 13:14:07 UTC  
> Url: https://github.com/boostorg/geometry/issues/585#issuecomment-482569700  

It seems I also made a mistake and pushed extensions directory into master.

---

## Comment 4

> Username: Kojoley  
> Created at: 2019-04-12 13:14:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/585#issuecomment-482569751  

Are they completely undocumented? Otherwise you can setup a redirection to a correct page.

---

## Comment 5

> Username: awulkiew  
> Created at: 2019-04-12 13:17:58 UTC  
> Url: https://github.com/boostorg/geometry/issues/585#issuecomment-482570988  

I did do that but it it seems it didn't work. It's possible that the path is wrong I guess.  
  
It's also questionable should these be on the list of libraries since technically they are not sub-libraries.

---

## Comment 6

> Username: awulkiew  
> Created at: 2019-04-12 13:20:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/585#issuecomment-482571788  

@Kojoley AFAICT redirect is the same as the one in Spirit and Functional:  
https://github.com/boostorg/geometry/blob/develop/index/index.html  
https://github.com/boostorg/spirit/blob/develop/repository/index.html  
https://github.com/boostorg/functional/blob/develop/factory/index.html

---

## Comment 7

> Username: awulkiew  
> Created at: 2019-04-12 13:23:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/585#issuecomment-482572619  

My understanding is that redirections in documentation are unrelated to the script generating this list:  
https://www.boost.org/doc/libs/1_70_0

---

## Comment 8

> Username: Kojoley  
> Created at: 2019-04-12 13:23:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/585#issuecomment-482572643  

Spirit sublibs has documentation https://github.com/boostorg/spirit/tree/develop/repository/doc/html, in your case you need to redirect backwards `../doc/html/index.html`

---

## Comment 9

> Username: awulkiew  
> Created at: 2019-04-12 13:25:18 UTC  
> Url: https://github.com/boostorg/geometry/issues/585#issuecomment-482573324  

Do you know of a way of testing it or do we have to wait for the next release?

---

## Comment 10

> Username: awulkiew  
> Created at: 2019-04-12 13:27:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/585#issuecomment-482573961  

And thanks for the help. It seems that it should work.

---

## Comment 11

> Username: Kojoley  
> Created at: 2019-04-12 13:27:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/585#issuecomment-482573984  

Generate the documentation (`b2 html`) and just open the `index.html` in your browser.

---

## Comment 12

> Username: Kojoley  
> Created at: 2019-04-12 13:32:23 UTC  
> Updated at: 2019-04-12 13:33:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/585#issuecomment-482575765  

> do we have to wait for the next release  
  
No, the docs are build on every commit by CI in the main repo. https://www.boost.org/doc/libs/develop/

---

## Comment 13

> Username: awulkiew  
> Created at: 2019-04-12 13:34:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/585#issuecomment-482576406  

Interesting. Ok, I'll handle it today.

---

## Comment 14

> Username: Kojoley  
> Created at: 2019-04-12 13:53:16 UTC  
> Url: https://github.com/boostorg/geometry/issues/585#issuecomment-482582942  

I do not know how it is working, but the list generator has `hidden` libraries https://github.com/boostorg/website/blame/master/doc/libraries.json.php#L15-L21

---

## Comment 15

> Username: awulkiew  
> Created at: 2019-04-12 14:27:07 UTC  
> Updated at: 2019-04-12 14:52:53 UTC  
> Url: https://github.com/boostorg/geometry/issues/585#issuecomment-482594873  

`is_hidden()` is implemented here I think:  
https://github.com/boostorg/website/blob/master/common/code/boost_version.php#L250  
  
EDIT: `hidden_stage` is set here:  
https://github.com/boostorg/website/blob/master/common/code/boost_version.php#L138  
  
but which `$version_string` is passed into `parseVersion()` I don't know. It may as well be some global version instead per-library string.

---

## Comment 16

> Username: Kojoley  
> Created at: 2019-04-16 00:27:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/585#issuecomment-483466284  

Did not see your edit.  
It is per library. To use it, put `"status": "hidden"` or `"boost-version": "hidden"` in a manifest. What I was saying in my previous message that I do not know the consequences of doing that.

---

## Comment 17

> Username: awulkiew  
> Created at: 2019-04-17 02:32:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/585#issuecomment-483913768  

Ok, I'll try that after seeing changes for the current version reflected on the libraries list for develop branch. Thanks.

---

## Comment 18

> Username: awulkiew  
> Created at: 2019-05-08 11:45:01 UTC  
> Url: https://github.com/boostorg/geometry/issues/585#issuecomment-490454191  

It looks like the redirection works. I'll now try to hide them.

---

## Comment 19

> Username: awulkiew  
> Created at: 2019-08-06 11:10:28 UTC  
> Url: https://github.com/boostorg/geometry/issues/585#issuecomment-518624505  

@Kojoley I tried setting `status` and both `status` and `boost-version` to `hidden` but sublibs are still on the list.

---

## Comment 20

> Username: mloskot  
> Created at: 2020-03-27 14:47:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/585#issuecomment-605040362  

There have been changes related to this issue and the current situation is this:  
  
1. Boost `develop` docs only include https://www.boost.org/doc/libs/develop#lib-geometry  
  
2. Boost `master` docs no longer include the `#lib-geometry-index` but still include the https://www.boost.org/doc/libs/master#lib-geometry-extensions  
    I suspect, this is due to the fact Geometry's `develop` has not been fully merged to `master` and once the merge happens, the `#lib-geometry-extensions` should be gone as well.  
  
Question is, if there is going to be `develop` to `master` merge before [closure of Boost 1.73 for major changes](https://lists.boost.org/Archives/boost/2020/03/248524.php).

---

## Comment 21

> Username: awulkiew  
> Created at: 2020-03-27 16:50:55 UTC  
> Url: https://github.com/boostorg/geometry/issues/585#issuecomment-605106308  

@mloskot All changes related to sublibs metadata were already merged into master. The master branch does not have extensions directory at all. By mistake I pushed extensions directory only with metadata into master at some point but I removed it later.  
  
So I don't know what's happening there. Does it pick up the sublibs directories from develop branch but then metadata from master branch? Is this documented anywhere? This thing starts to annoy me and I'm strongly considering removing all of the metadata from the sublibs directories and to go back to ignoring the related regression matrix errors.

---

## Comment 22

> Username: mloskot  
> Created at: 2020-03-27 19:54:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/585#issuecomment-605288286  

@awulkiew I have no idea what is going on, how it works, but I've just asked for help here [[geometry] Global Boost documentation for master not refreshed?](https://lists.boost.org/Archives/boost/2020/03/248531.php)

---

## Comment 23

> Username: mloskot  
> Created at: 2020-04-15 20:28:06 UTC  
> Updated at: 2020-04-15 20:52:07 UTC  
> Url: https://github.com/boostorg/geometry/issues/585#issuecomment-614263295  

My thread has received no answers and the problem is still happening in 1.73 beta1:  
  
1. Go to https://lists.boost.org/Archives/boost/2020/04/248687.php  
2. Download `boost_1_73_0_b1.7z`  
3. Unpack and load `boost_1_73_0/index.html`  
4. Go to `boost_1_73_0/libs/libraries.htm`  
5. Hit the "Geometry Extensions" link  
6. Loaded is `/boost_1_73_0/libs/geometry/extensions/index.html` which does not exist  
  
I'm going to re-ask on the mailing list. See https://lists.boost.org/Archives/boost/2020/04/248727.php

---

## Comment 24

> Username: tinko92  
> Created at: 2020-04-17 09:41:07 UTC  
> Updated at: 2020-04-17 09:51:49 UTC  
> Url: https://github.com/boostorg/geometry/issues/585#issuecomment-615150173  

The following observations are not backed by a complete understanding of the website and the way it is updated, it is based on trying to run the website locally, and I hope it may be helpful:  
Consider https://www.boost.org/doc/libraries.xml , which I believe is generated using https://github.com/boostorg/website/blob/master/site-tools/update-doc-list.php :  
We find:   
- geometry/extensions, update version 1.70, not hidden  
- geometry/extensions, update version develop, hidden  
- geometry/index, update version 1.70, not hidden  
- geometry/index, later update versions, hidden  
  
In the documentation we find:  
- version 1.70: extensions and index show up  
- version 1.72: extensions shows up, index does not  
  
running https://github.com/boostorg/website/blob/master/doc/generate.php locally and manually inserting an entry into $libs for version 1.72 or 1.73 with extensions and status hidden, makes the entry go away in my local output of https://www.boost.org/doc/generate.php?page=libs/libraries.htm&version=1.73 .  
  
This suggests, that for generating the list for some numbered version the latest metadata from a release version is used. So having 1.70 as the latest metadata for geometry extensions and having none after that may be the issue. Maybe if there was metadata for extensions with status hidden in release 1.73, that would make it go away for future releases.

---

## Comment 25

> Username: tinko92  
> Created at: 2020-04-17 22:59:55 UTC  
> Url: https://github.com/boostorg/geometry/issues/585#issuecomment-615499275  

Minor followup. By applying the following steps I was able to make the "geometry/extension" disappear in the output of generate.php?page=libs/libraries.html&version=1.72 :   
1. clone boostorg/website, set it up with local mirrors of all boost repositories on local webserver  
2. clone local mirror of boostorg/boost.git recursively, commit and push this change ( https://github.com/boostorg/geometry/compare/master...tinko92:fix/doc_extension , I don't know if this would have other side-effects, so I didn't create a PR yet) to geometry-submodule  
3. tag it as 1.72 (overwriting old tag) in the superproject, push superproject, run site-tools/update-doc-list.php on local mirrors of repository on branch master  
4. run generate.php?page=libs/libraries.htm&version=1.72  --> no geometry extension entry.  
  
So, I believe if the meta data would be added like in my branch linked above, and be in the tag for 1.73 in the superproject, then this would get rid of the entry. Caveat: I don't know if those metadata is used for other purposes than making lists of libraries, so my proposed fix may be invalid.

---

## Comment 26

> Username: glenfe  
> Created at: 2020-04-18 04:09:25 UTC  
> Url: https://github.com/boostorg/geometry/issues/585#issuecomment-615552676  

https://github.com/boostorg/website/commit/d8c79ba5cb78729cfe02bfbecd9a74f834de7b9f and https://github.com/boostorg/website/commit/8d463e641c6007acd128ee14bf08070c4dbd08c3 .
