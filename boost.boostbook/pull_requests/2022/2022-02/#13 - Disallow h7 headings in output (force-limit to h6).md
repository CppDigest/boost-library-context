# #13 Disallow h7 headings in output (force-limit to h6) [Merged]

> Username: evanlenz  
> Created at: 2022-02-05 21:29:44 UTC  
> Updated at: 2024-09-25 07:29:42 UTC  
> Merged at: 2022-02-28 22:26:36 UTC  
> Closed at: 2022-02-28 22:26:36 UTC  
> Url: https://github.com/boostorg/boostbook/pull/13  

fix #12

---

## Comment 1

> Username: glenfe  
> Created_at: 2022-02-06 19:06:27 UTC  
> Url: https://github.com/boostorg/boostbook/pull/13#issuecomment-1030893981  

Where is h7 even coming from ? Quickbook only has up to h6, and nested sections even 7+ levels deep never generate a h7

---

## Comment 2

> Username: evanlenz  
> Created_at: 2022-02-06 20:34:58 UTC  
> Url: https://github.com/boostorg/boostbook/pull/13#issuecomment-1030909078  

They do if `<bridgehead>` comes into play in the BoostBook output passed to the DocBook stylesheets. The code in the pull request, without the called-out modification, can generate `<h7>` or `<h8>` by incrementing once or twice to `<h6>` (which the DocBook stylesheets correctly limit elsewhere).  
  
`$clevel` on this (commented-out) line can be 6 or 7 (if `$slevel` is already 6):  
https://github.com/boostorg/boostbook/pull/13/commits/116c7b0f23b578ece6f5c530584a987f4db6947b#diff-27e6e7695d90cbd92ac9edc42cf23d2f6e08b3b3aeb77c59861f9c7b4e8534aaR97

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2022-02-09 00:48:42 UTC  
> Url: https://github.com/boostorg/boostbook/pull/13#issuecomment-1033211829  

What is the next step?

---

## Comment 4

> Username: evanlenz  
> Created_at: 2022-02-09 02:50:53 UTC  
> Url: https://github.com/boostorg/boostbook/pull/13#issuecomment-1033289569  

I found my Boost developer list subscription confirmation message in my spam folder, confirmed my subscription, and sent a request just now asking for someone familiar with BoostBook to review this pull request.

---

## Comment 5

> Username: evanlenz  
> Created_at: 2022-02-28 19:18:25 UTC  
> Url: https://github.com/boostorg/boostbook/pull/13#issuecomment-1054581747  

@glenfe Are you planning to merge this PR? Is there anything else you need first?

---

## Comment 6

> Username: glenfe  
> Created_at: 2022-02-28 22:27:50 UTC  
> Url: https://github.com/boostorg/boostbook/pull/13#issuecomment-1054723772  

If this causes problems in any Boost library's documentation we will revert it, and look for some opt-in solution that Beast/Json/Url can use.

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2024-09-22 16:51:08 UTC  
> Url: https://github.com/boostorg/boostbook/pull/13#issuecomment-2366869316  

Why is this merged without reviews - from a contributor never committed to Boost before?

---

## Comment 8

> Username: vinniefalco  
> Created_at: 2024-09-23 01:12:56 UTC  
> Url: https://github.com/boostorg/boostbook/pull/13#issuecomment-2367082116  

Evan's done quite a bit of work improving the docca tool (https://github.com/boostorg/docca).  Is this changing causing a problem?

---

## Comment 9

> Username: barendgehrels  
> Created_at: 2024-09-23 06:24:20 UTC  
> Url: https://github.com/boostorg/boostbook/pull/13#issuecomment-2367321758  

Cool. I'm was actually not questioning Evan, but the procedure.  
* A PR is submitted (great)  
* the PR is not only fixing the problem (nice) but also refactoring other thing(s)  
* it was the first PR for Boost from this contributor  
* it is not approved  
* it is then merged with the statement: if it causes problems, it will be reverted.  
  
But it's not just compilation where things are unit tested and you see problems immediately.  
If the documentation breaks, it is often not noticed by maintainers because they don't run it themselves for every release.  
  
In my case (Boost.Geometry) the documentation is still on the site, and looks up-to-date. But local generation indicates that it is broken already for two years (and indeed I did not run it myself for two years).  
  
So I think this needs some attention.  
  
And you noticed it yourself too, here https://github.com/boostorg/boostbook/issues/17  
Bumping up indeed helps specifically this problem.  
But the version cannot be bumped up (without other changes), because that breaks the documentation in another way - at least for Boost.Geometry...

---

## Comment 10

> Username: glenfe  
> Created_at: 2024-09-23 11:02:47 UTC  
> Updated_at: 2024-09-23 11:11:50 UTC  
> Url: https://github.com/boostorg/boostbook/pull/13#issuecomment-2367887464  

Boostbook is currently unmaintained and we had no volunteers to either take over its maintenance (or even review this PR for that matter).  
  
In the absence of a maintainer/reviewer, we discussed what to do about this PR on Slack and the conclusion was a release manager would merge it, and revert it if it caused problems. This is normal for any tools to unblock issues ( in this case with the aforementioned three libraries' documentation).  
  
@vinniefalco If you and Barend can't come to any sort agreement about this, my comment above stands -please submit a PR to revert the change.  
  
@barendgehrels If you would like to take over maintenance of boostbook, that can be arranged following a quick discussion on the ML.

---

## Comment 11

> Username: vinniefalco  
> Created_at: 2024-09-23 14:02:26 UTC  
> Updated_at: 2024-09-23 14:07:09 UTC  
> Url: https://github.com/boostorg/boostbook/pull/13#issuecomment-2368366088  

Quickbook is an existential risk to any libraries which use it, because the tool is unmaintained. This is exactly the problem I was afraid of, that we would have a breakage that is unfixable. I am okay with reverting this change if it fixes Geometry, as we are migrating all of our libraries to Antora or Asciidoctor for this reason.  
  
@barendgehrels the long term solution is to move off Quickbook. Since Boost.Geometry is using multi-page documentation how would you feel about switching to Antora? It uses Asciidoctor markdown. I see Geometry is using a doxygen-generated reference, we have support for that both through docca and through Mr. Docs (which you might enjoy over Doxygen).  
  
This is what an Antora/MrDocs generated documentation site looks like (note that we are still polishing the CSS)  
https://www.boost.org/doc/libs/develop/doc/antora/url/index.html  
  
Here is the reference  
https://www.boost.org/doc/libs/develop/doc/antora/url/reference.html  
  
If you would like to discuss this further consider joining the Official C++ Language Slack Workspace, sign up at https://cpp.al we are in the `#boost` channel.

---

## Comment 12

> Username: barendgehrels  
> Created_at: 2024-09-23 20:34:06 UTC  
> Url: https://github.com/boostorg/boostbook/pull/13#issuecomment-2369326096  

In the meantime I found out that `1.79.1` gives perfect documentation, as it was before (So `1.79.2` is problematic - and the current `1.75.2` is broken for multiple libraries).  
Fixing 1.75.2 is easy (it needs to delete just 3 lines from our `sections.xsl`). If you want I can make a PR. Then a full revert is not necessary.  
  
@glenfe clear, thanks for the explanation. But I don't have time enough already for Boost.Geometry - there are still many issues open. And I don't have xsl knowlegde. So I can't take over maintenance. However, I'm willing to contribute to fixing just this current problem.  
  
@vinniefalco basically I love the quickbook approach. It's a pity it is not maintained. Many libraries use quickbook. We have our own program to convert doxygen xml to quickbook and it works perfectly, already for 12 years or so, almost without any maintenance... But what you mention also looks good and is interesting. I will have a closer look.

---

## Comment 13

> Username: barendgehrels  
> Created_at: 2024-09-23 20:40:58 UTC  
> Updated_at: 2024-09-23 20:41:10 UTC  
> Url: https://github.com/boostorg/boostbook/pull/13#issuecomment-2369340159  

So we can either  
* fix `1.75.2` (3 lines)  
* bump versions to `1.79.1` (adapting the script)  
  
The last is a bit more work but I see that several documentation on the site (including Boost.Geometry), or maybe all, uses that, so that might be a better choice.  
When I made the first comment I was not aware that the published documentation uses `1.79.1` but locally it still gives you `1.75.2`, because that is built in. So the problem is actually smaller than I thought before.

---

## Comment 14

> Username: vinniefalco  
> Created_at: 2024-09-23 20:48:10 UTC  
> Url: https://github.com/boostorg/boostbook/pull/13#issuecomment-2369352877  

> @vinniefalco basically I love the quickbook approach. It's a pity it is not maintained.  
  
Yep. All my docs are in Quickbook so I understand. Although, Alan has ported Boost.URL to Antora so things have started at least.  
  
> Many libraries use quickbook.  
  
Yes it is the most popular doc toolchain for Boost libraries. We might try to write a converter, although it might also need some manual fixes afterwards.  
  
> We have our own program to convert doxygen xml to quickbook  
  
That is the approach I used as well, which I copied from Boost.Asio. "docca" is a an XSL program which transforms Doxygen's XML into Quickbook. Quite a bit of work went into it, and it is better than the transformation you get from the Bjam doxygen module:  
  
https://github.com/boostorg/docca  
  
@grisumbras has developed a replacement for docca which uses Python instead of xlstproc. This is great because no one understands how to write in XSL, but many know how to write Python. His version already does everything that docca does, and now more such as understanding noexcept and grouping of overloads.  
  
pydocca is part of the docca repo so it is easy to switch:  
  
https://github.com/boostorg/docca/blob/develop/docca.py  
  
Mr. Docs is ultimately the long-term solution. It is a tool which uses clang/LLVM to parse the C++ code into an AST tree, and it understands javadoc comments. A plugin system then renders the resulting set of declarations. It supports XML, and Asciidoc. The Asciidoc renderer uses Handlebars templates and so can be fully customized. Or you can import the XML into a Python program and use Jinja templates.  
  
Mr. Docs is very flexible and it understands the latest C++ constructs, since it is built on the latest version of clang. You cannot emit Quickbook though, and I don't think we will ever invest much work into doing that such as you have noticed the toolchain is not well maintained. It might be accurate to use the term "end of life." You could take the XML from Mr. Docs and produce Quickbook with it with a bit of work though. And Mr. Docs XML is very clean and contains every possible bit of information that we can get from clang's AST, unlike Doxygen which has many flaws in its XML.

---

## Comment 15

> Username: Lastique  
> Created_at: 2024-09-23 22:45:24 UTC  
> Url: https://github.com/boostorg/boostbook/pull/13#issuecomment-2369678434  

It's unfortunate that we're hijacking this PR for the discussion, I'm sorry about that. The proper way to do this would be to create an issue and discuss it there; @barendgehrels please do this in the future. I'm continuing the discussion here for the sake of keeping it in one place, even though the place is not appropriate.  
  
As I commented in the referenced commit, I don't think we want to remove the lines calling the `id.attribute` template. If DocBook XSL 1.75.2 doesn't support it, I'm inclined to not support that version.  
  
Bumping DocBook XSL version is proposed in https://github.com/boostorg/boostbook/pull/19, but the changes needed there are not sufficient since the archives are not available at the locations specified in the scripts, or they weren't last time I checked. The scripts need more involved changes than a mere version bump. I wanted to do that at some point but got distracted. Though I think, it's pretty low priority since, as I understand, those scripts are not used by the release process (to build the docs that go on the website) and probably not used by users (on Linux people usually just install the stylesheets from system packages; I'm not sure what's the work flow on Windows).  
  
I would like to know more about problems with DocBook XSL 1.79.2. This is the version supplied in recent Ubuntu versions, and I'm assuming other distros as well, and I would like to maintain compatibility with it. I'm currently trying to build Boost.Geometry docs, but I'm having trouble with it as the build process fails because it can't find the `doxygen_xml2qbk` tool.  
  
```  
sh: 1: doxygen_xml2qbk: not found  
Boost.Geometry is making .qbk files in /home/lastique/src/boost/libs/geometry/doc  
Boost.Geometry is cleaning Doxygen files in /home/lastique/src/boost/libs/geometry/doc/doxy/doxygen_output/xml/  
Boost.Geometry is cleaning Doxygen files in /home/lastique/src/boost/libs/geometry/doc/index/xml/  
Traceback (most recent call last):  
  File "/home/lastique/src/boost/libs/geometry/doc/make_qbk.py", line 209, in <module>  
    exec(compile(open("make_qbk.py", "rb").read(), "make_qbk.py", 'exec'))  
  File "make_qbk.py", line 38, in <module>  
    run_command(cmd % ("classboost_1_1geometry_1_1index_1_1rtree", "rtree"))  
  File "make_qbk.py", line 27, in run_command  
    raise Exception("Error running %s" % command)  
Exception: Error running doxygen_xml2qbk --xml xml/classboost_1_1geometry_1_1index_1_1rtree.xml --start_include boost/ --output_style alt --alt_max_synopsis_length 59 > generated/rtree.qbk  
```

---

## Comment 16

> Username: Lastique  
> Created_at: 2024-09-23 22:55:18 UTC  
> Updated_at: 2024-09-23 22:59:30 UTC  
> Url: https://github.com/boostorg/boostbook/pull/13#issuecomment-2369701806  

@barendgehrels What do you mean by "broken chunking" with DocBook XSL 1.79.2? I have just built Boost.Geometry docs on Kubuntu 24.04 with system DocBook XSL (which is version 1.79.2+dfsg-7) and as far as I can see the docs look exactly the same as on the current website [here](https://www.boost.org/doc/libs/1_86_0/libs/geometry/doc/html/index.html).  
  
Also, are you sure you are building Boost.Geometry from a clean git checkout and are not viewing some cached content in the web browser? Make sure you remove `bin.v2` in Boost root and also clean `libs/geometry` (e.g. by `git reset --hard`).  
  
PS: In order to complete the build, I had to copy `doxygen_xml2qbk` to a directory that is in my `$PATH`. I think, there is a problem in Boost.Geometry build scripts if this is required.

---

## Comment 17

> Username: Lastique  
> Created_at: 2024-09-23 23:11:57 UTC  
> Url: https://github.com/boostorg/boostbook/pull/13#issuecomment-2369738925  

@vinniefalco Please, let's keep this discussion more focused on BoostBook and not other tools. I understand that the other tools may provide subjectively better results, but discussing them here doesn't help with BoostBook issues. ML or Slack might be a better place for it.

---

## Comment 18

> Username: barendgehrels  
> Created_at: 2024-09-24 06:34:48 UTC  
> Url: https://github.com/boostorg/boostbook/pull/13#issuecomment-2370313699  

@Lastique It is broken in the sense that , for example, the generated "**geometry/reference/adapted.html**" is empty, it does not contain a directory listing like ([here](https://www.boost.org/doc/libs/1_86_0/libs/geometry/doc/html/geometry/reference/adapted.html)). And therefore many mutual links are broken. Indeed the TOC is right and the reference pages themselves are also right, as is the matrix and the index.  
  
No, Boost.Geometry's Jamfile is not broken and I cleaned my cache, and Boost geometry is clean and I'm using the recent `b2`.  
  
I can perfectly reproduce it, I actually spent nearly two days on this specific problem.  
Such that I've now also a shell script doing the things without `b2` - but the problem was not there. But I can call only docbook to verify exactly what happens where.  
  
Anyway, thanks for your answer and even trying to make our docs!  
  
`1.79.2+dfsg-7` - maybe that makes a difference, I got `1.79.2` from the github website. I'm on Mac.

---

## Comment 19

> Username: barendgehrels  
> Created_at: 2024-09-24 06:36:14 UTC  
> Updated_at: 2024-09-24 06:39:13 UTC  
> Url: https://github.com/boostorg/boostbook/pull/13#issuecomment-2370315886  

> @barendgehrels please do this in the future.  
  
Well, I just asked here why it was merged without being approved...  
  
Note that part of the discussion is also https://github.com/boostorg/boostbook/issues/17   
  
Anyway, I'm fine to move to a central place, it's indeed fragmented now and "hijacked" in an old PR.

---

## Comment 20

> Username: Lastique  
> Created_at: 2024-09-24 08:58:15 UTC  
> Updated_at: 2024-09-24 09:09:36 UTC  
> Url: https://github.com/boostorg/boostbook/pull/13#issuecomment-2370680818  

> @Lastique It is broken in the sense that , for example, the generated "**geometry/reference/adapted.html**" is empty, it does not contain a directory listing like ([here](https://www.boost.org/doc/libs/1_86_0/libs/geometry/doc/html/geometry/reference/adapted.html)). And therefore many mutual links are broken.  
  
My locally built `libs/geometry/doc/html/geometry/reference/adapted.html` looks the same as the page you referenced on the website.  
  
> No, Boost.Geometry's Jamfile is not broken and I cleaned my cache, and Boost geometry is clean and I'm using the recent `b2`.  
  
What I mean is that the user should not have to put `doxygen_xml2qbk` in his `$PATH` for the build to work. It should work on arbitrary system by just running `b2`, which didn't work on my system.  
  
What I think happens is, the path to `doxygen_xml2qbk` that is passed to `make_qbk.py` by the `Jamfile` is relative from `libs/geometry/doc`:  
  
```  
"/home/lastique/src/boost/libs/geometry/doc/make_qbk.py" --release-build --doxygen-xml2qbk "../../../bin.v2/libs/geometry/doc/src/docutils/tools/doxygen_xml2qbk/gcc-13/release/x86_64/link-static/threading-multi/visibility-hidden/doxygen_xml2qbk" > "../../../bin.v2/libs/geometry/doc/make_qbk.test/gcc-13/release/x86_64/link-static/threading-multi/visibility-hidden/make_qbk.output" 2>&1 < /dev/null  
```  
  
But `make_qbk.py` performs a `chdir` into `index` [here](https://github.com/boostorg/geometry/blob/6c173505d9e56f0ff0c764204f03022e88f678b5/doc/make_qbk.py#L208), which makes the relative path incorrect for the following `doxygen_xml2qbk` execution.  
  
I will create an issue for this.  
  
> `1.79.2+dfsg-7` - maybe that makes a difference, I got `1.79.2` from the github website. I'm on Mac.  
  
The [Ubuntu packages](https://packages.ubuntu.com/noble/docbook-xsl) are built from the same sources as the packages on GitHub [here](https://github.com/docbook/xslt10-stylesheets/releases/tag/release%2F1.79.2), only with `extensions` and `webhelp` directories removed. Then a few patches are applied, which can be seen in [this archive](http://archive.ubuntu.com/ubuntu/pool/universe/d/docbook-xsl/docbook-xsl_1.79.2+dfsg-7.debian.tar.xz), in `debian/patches`. None of these patches seem relevant to me.  
  
Perhaps, there is a problem with multiple DocBook XSL installations on your system? It could be that XML catalog on your system points to one DocBook XSL version and you're configuring `b2` for another? This would make both versions used by `xsltproc`, depending on whether the path to the stylesheet uses an URL or not.

---

## Comment 21

> Username: Lastique  
> Created_at: 2024-09-24 09:22:42 UTC  
> Url: https://github.com/boostorg/boostbook/pull/13#issuecomment-2370740562  

> The [Ubuntu packages](https://packages.ubuntu.com/noble/docbook-xsl) are built from the same sources as the packages on GitHub [here](https://github.com/docbook/xslt10-stylesheets/releases/tag/release%2F1.79.2), only with `extensions` and `webhelp` directories removed. Then a few patches are applied, which can be seen in [this archive](http://archive.ubuntu.com/ubuntu/pool/universe/d/docbook-xsl/docbook-xsl_1.79.2+dfsg-7.debian.tar.xz), in `debian/patches`. None of these patches seem relevant to me.  
  
On the second thought, one of the patches updates the canonical URLs in the XML catalog to be the same as 1.79.1 and before. In other words, it makes old URLs to DocBook XSL stylesheets refer to the stylesheets installed on the system by the package. Without this change, old URLs would result in pulling the stylesheets from the Internet, if those URLs are still alive.  
  
What I'm saying is if you indeed have multiple DocBook XSL versions installed, your XML catalog should redirect both old and new URLs to your 1.79.2 installation. Otherwise you may get some stylesheets from one version and other from another one.

---

## Comment 22

> Username: barendgehrels  
> Created_at: 2024-09-24 16:18:48 UTC  
> Url: https://github.com/boostorg/boostbook/pull/13#issuecomment-2371751368  

Good points. I indeed have multiple XSL's (3 now). But I updated the XML catalog manually (I yesterday found out that that is indeed where b2 caches what it gets from user-config.jam) and then I still have the problem.  
  
> My locally built libs/geometry/doc/html/geometry/reference/adapted.html looks the same as the page you referenced on the website.  
  
Thanks for confirming. And if that is the case, maybe it's indeed on my side only then... somehow. And if that is the case, we can close the issues - because I can use `1.79.1` locally and update the documentation as wished. Or even use `1.75.2` and apply my own hack locally. If the release documentation is fine, and I can locally see what is generated, it's fine.  
  
Fine but not ideal, I'm still curious where is the exact problem... Will try a bit more. Thanks for your help.

---

## Comment 23

> Username: barendgehrels  
> Created_at: 2024-09-24 17:19:41 UTC  
> Url: https://github.com/boostorg/boostbook/pull/13#issuecomment-2371877998  

So, after rerunning it several times, I can still reproduce the "problem" for 1.79.2. Problem between quotes, because 1.79.1 is used by Boost releases (found this now, very [useful](https://github.com/boostorg/release-tools/blob/develop/build_docs/macosdocs.sh#L330-L343)).  
  
1.79.1 gives, for adapted.html  
```  
=== Resulting contents of generated 'adapted.html', there should be sections  
<div class="section">  
<dt><span class="section"><a href="adapted/c_array.html">C array</a></span></dt>  
<dt><span class="section"><a href="adapted/std_array.html">C++11 Array Container</a></span></dt>  
<dt><span class="section"><a href="adapted/boost_array.html">Boost.Array</a></span></dt>  
<dt><span class="section"><a href="adapted/boost_fusion.html">Boost.Fusion</a></span></dt>  
<dt><span class="section"><a href="adapted/boost_tuple.html">Boost.Tuple</a></span></dt>  
<dt><span class="section"><a href="adapted/boost_polygon.html">Boost.Polygon</a></span></dt>  
<dt><span class="section"><a href="adapted/boost_range.html">Boost.Range</a></span></dt>  
<dt><span class="section"><a href="adapted/register.html">Macro's for adaption</a></span></dt>  
```  
  
1.79.2 gives just:  
```  
<div class="section">  
```  
  
Using `b2` the reproduction is the same. If I change `1.79.1` into `1.79.2` in my `user-config.jam`, the sections disappear.

---

## Comment 24

> Username: Lastique  
> Created_at: 2024-09-24 22:23:34 UTC  
> Updated_at: 2024-09-24 22:24:02 UTC  
> Url: https://github.com/boostorg/boostbook/pull/13#issuecomment-2372493945  

@barendgehrels Can you try running the build with DocBook XSL versions other than 1.79.2 removed from your system?

---

## Comment 25

> Username: Lastique  
> Created_at: 2024-09-25 01:37:36 UTC  
> Url: https://github.com/boostorg/boostbook/pull/13#issuecomment-2372703089  

I have set up a CI [here](https://github.com/Lastique/geometry/actions/runs/11024497731/job/30617764464) that downloads and builds Boost.Geometry docs using vanilla DocBook XSL 1.79.2 and DocBook XML 4.5 packages. The `adapted.html` check passes. You can see the workflow file [here](https://github.com/Lastique/geometry/blob/d700586b03660f24a0fa154b313b123bc86641e0/.github/workflows/docbook_1_79_2.yml). The workflow relies on https://github.com/boostorg/geometry/pull/1312 to work, but otherwise Boost.Geometry is unchanged.  
  
The important factor that makes the test succeed is that I'm using the "docbook-xsl-nons" package rather than the default "docbook-xsl". In Ubuntu packages, the "nons" version is actually the default and is called "docbook-xsl". The upstream "docbook-xsl" package is named "docbook-xsl-ns" in Ubuntu. Boost documentation is always built on Ubuntu using the Ubuntu's "docbook-xsl" package.  
  
I think, the difference between the two versions of DocBook XSL is that the "ns" version uses XML namespaces and is compatible with DocBook 5. The older "nons" version is compatible with DocBook 4, which is what we use in Boost. I'm not sure why the "ns" version of DocBook XSL 1.79.1 works. Perhaps, it is somehow still compatible with DocBook 4, but I'm not sure if that compatibility is intentional and not by accident. In any case, I think it is more correct to use the "nons" version to build Boost docs.

---

## Comment 26

> Username: barendgehrels  
> Created_at: 2024-09-25 07:29:41 UTC  
> Url: https://github.com/boostorg/boostbook/pull/13#issuecomment-2373278727  

🎉 Cool! the "nons" version of `1.79.2` indeed works for me! So that was the problem!  
Thanks a lot. I was not even aware that there exists a no-namespace version.  
It's probably related to one message I noticed, `Note: namesp. add : added namespace before processing   Geometry`, I should have mentioned that in hindsight.  
  
Thanks a lot for all your help, research, the local ci, finding the clue, and the PR! I'm really happy!

---
