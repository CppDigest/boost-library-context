# #14 - Does not work with libxslt 1.1.35 [Closed]

> Username: giomasce  
> Created at: 2022-08-19 19:40:05 UTC  
> Updated at: 2023-02-10 22:20:47 UTC  
> Closed at: 2023-02-10 22:20:47 UTC  
> Url: https://github.com/boostorg/boostbook/issues/14  

(This was initially posted to [the Boost mailing list](https://lists.boost.org/Archives/boost/2022/08/253369.php), but maybe this is a more specific location)  
  
Hi, it seems that Boost fails building boostbook docs if xsltproc and libxslt1 version 1.1.35 is installed (it works with version up to 1.1.34).  
  
Specifically, I can reproduce the failure following these steps:  
* recursively clone `git@github.com:boostorg/boost.git`  
* `./bootstrap.sh`  
* `echo "using boostbook ;" > user-config-doc.jam`  
* `./b2 -j32 -q -d2 --ignore-site-config --enable-index --user-config=user-config-doc.jam --build-dir=build-doc`  
* `./b2 -j32 -q -d2 --ignore-site-config --enable-index --user-config=user-config-doc.jam --build-dir=build-doc doc`  
  
On my Debian unstable system this works when `xsltproc` and `libxslt1.1` version 1.1.34 are installed, but fails when 1.1.35 is installed, with the following error:  
```  
"xsltproc" --stringparam boost.defaults "Boost" --stringparam boost.root "../../../.." --path "build-doc/boost/bin.v2/libs/logic/doc/docbook" --xinclude -o "build-doc/boost/bin.v2/libs/logic/doc/docbook/tribool.docbook" "/<<PKGBUILDDIR>>/tools/boostbook/xsl/docbook.xsl" "/<<PKGBUILDDIR>>/libs/logic/doc/tribool.boostbook"  
runtime error: file /<<PKGBUILDDIR>>/tools/boostbook/xsl/annotation.xsl line 432 element element  
xsl:element: The effective name '' is not a valid QName.  
runtime error: file /<<PKGBUILDDIR>>/tools/boostbook/xsl/annotation.xsl line 432 element element  
xsl:element: The effective name '' is not a valid QName.  
runtime error: file /<<PKGBUILDDIR>>/tools/boostbook/xsl/annotation.xsl line 432 element element  
xsl:element: The effective name '' is not a valid QName.  
[repeated many times]  
```  
  
Many different libraries are failing in the same way, so I guess that the real problem is in boostbook. However I don't know enough of XSLT and `libxslt` to give a proper diagnosis.  
  
By bisection I found that the first commit in `libxslt` causing the failure is https://gitlab.gnome.org/GNOME/libxslt/-/commit/b0074eeca3c6b21b4da14fdf712b853900c51635.  
  
For the context, this bug was discovered [during a test rebuild of the Debian package for Boost](https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=1016321). However it can be reproduced with master doing the steps above.  
  
Thanks in advance for any help you can provide, Giovanni.

---

## Comment 1

> Username: evanlenz  
> Created at: 2022-08-22 22:40:31 UTC  
> Url: https://github.com/boostorg/boostbook/issues/14#issuecomment-1223271017  

Excellent sleuthing. It's an easy fix from here. I'll comment more after I create a pull request.

---

## Comment 2

> Username: evanlenz  
> Created at: 2022-08-23 19:05:47 UTC  
> Updated at: 2022-08-23 19:11:29 UTC  
> Url: https://github.com/boostorg/boostbook/issues/14#issuecomment-1224642863  

The issue has almost the same suspect code as in the sample shown here: https://gitlab.gnome.org/GNOME/libxslt/-/issues/55  
  
Two template rules with the same mode ("annotation" in our case) matched `text()` and `node()`, respectively. The problem is that `node()` is effectively short for `* | comment() | text() | processing-instruction()` (where `*` just matches elements), and those two patterns (`text()` and `node()`) have the same [default priority (-0.5)](https://lenzconsulting.com/how-xslt-works/#priority) as well as the same import precedence (since they're both in annotation.xsl). This means that a text node will match both template rules and give rise to an error condition. As quoted in the [fix for xsltproc](https://gitlab.gnome.org/GNOME/libxslt/-/commit/b0074eeca3c6b21b4da14fdf712b853900c51635), the XSLT processor can choose to signal an error or recover by choosing the last-occurring template rule in the stylesheet. In our case, the `node()` rule came last. In libxslt 1.1.34 and earlier, xsltproc was not recovering properly and was firing the `text()` rule (likely according to the intentions of the stylesheet author). But once they fixed the xsltproc bug, the stylesheet bug became evident. If you look at the rule matching `node()`, you can see that it's designed to match and replicate elements. But when the matched node is a text node (which has no name), the element constructor will fail:  
  
```  
    <xsl:element name="{name(.)}">  
```  
  
giving rise to the error we saw: `xsl:element: The effective name '' is not a valid QName.`  
  
One band-aid fix, of course, would be to change the order of the template rules in the stylesheet, but that would be relying on error recovery, a bad practice, because XSLT processors are within their right to not recover. In other words, it's bad practice to rely on template rule order. So instead, we just need to modify the stylesheet to better reflect the author's original intention (match elements), by changing the pattern to match="*"; since the sets of nodes matching `*` and `text()` are disjoint, we no longer have a conflict.  
  
There is already a rule that matches `comment()` too, and that would have triggered the same error if a text node hadn't triggered it first.  
  
There isn't a rule that matches `processing-instruction()`, so I had to decide what to do about that. The built-in rule is to do nothing (i.e. output nothing). The existing behavior was to (effectively) output an empty element whose name is the same as the PI target (e.g. `<?foobar other text here?>` would get converted to `<foobar/>`). That almost certainly wasn't the stylesheet author's intention, but just to make sure, I scoured the Boost libraries for docs that might somehow be depending on that behavior and didn't find any. So the behavior change should be harmless.
