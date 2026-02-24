# #99 - Ambiguous rule match [Closed]

> Username: vinniefalco  
> Created at: 2021-10-10 00:58:23 UTC  
> Updated at: 2021-10-12 02:49:20 UTC  
> Closed at: 2021-10-12 02:49:20 UTC  
> Url: https://github.com/boostorg/docca/issues/99  

I noticed after merging the change to hide Description when there is no subsequent text, that these warnings are happening:  
```  
  XTDE0540: Ambiguous rule match for  
  /doxygen/compounddef[1]/sectiondef[1]/memberdef[1]/d:referenced-typedef-class[1]/compounddef[1]/detaileddescription[1]  
Matches both "element(Q{http://github.com/vinniefalco/docca}referenced-typedef-class)/element(Q{}compounddef)/element(Q{}detaileddescription)" on line 461 of file:/C:/Users/vinnie/src/boost/bin.v2/libs/url/doc/_reference-dir/base-stage1.xsl  
and "element(Q{}detaileddescription)[not(normalize-space(convertUntyped(data(.))))]" on line 216 of file:/C:/Users/vinnie/src/boost/bin.v2/libs/url/doc/_reference-dir/base-stage1.xsl  
Warning  
  XTDE0540: Ambiguous rule match for  
  /doxygen/compounddef[1]/sectiondef[1]/memberdef[1]/d:referenced-typedef-class[1]/compounddef[1]/detaileddescription[1]  
Matches both "element(Q{http://github.com/vinniefalco/docca}referenced-typedef-class)/element(Q{}compounddef)/element(Q{}detaileddescription)" on line 461 of file:/C:/Users/vinnie/src/boost/bin.v2/libs/url/doc/_reference-dir/base-stage1.xsl  
and "element(Q{}detaileddescription)[not(normalize-space(convertUntyped(data(.))))]" on line 216 of file:/C:/Users/vinnie/src/boost/bin.v2/libs/url/doc/_reference-dir/base-stage1.xsl  
Warning  
  XTDE0540: Ambiguous rule match for  
  /doxygen/compounddef[1]/sectiondef[1]/memberdef[1]/d:referenced-typedef-class[1]/compounddef[1]/detaileddescription[1]  
Matches both "element(Q{http://github.com/vinniefalco/docca}referenced-typedef-class)/element(Q{}compounddef)/element(Q{}detaileddescription)" on line 461 of file:/C:/Users/vinnie/src/boost/bin.v2/libs/url/doc/_reference-dir/base-stage1.xsl  
and "element(Q{}detaileddescription)[not(normalize-space(convertUntyped(data(.))))]" on line 216 of file:/C:/Users/vinnie/src/boost/bin.v2/libs/url/doc/_reference-dir/base-stage1.xsl  
Warning  
  XTDE0540: Ambiguous rule match for  
  /doxygen/compounddef[1]/sectiondef[1]/memberdef[1]/d:referenced-typedef-class[1]/compounddef[1]/detaileddescription[1]  
Matches both "element(Q{http://github.com/vinniefalco/docca}referenced-typedef-class)/element(Q{}compounddef)/element(Q{}detaileddescription)" on line 461 of file:/C:/Users/vinnie/src/boost/bin.v2/libs/url/doc/_reference-dir/base-stage1.xsl  
and "element(Q{}detaileddescription)[not(normalize-space(convertUntyped(data(.))))]" on line 216 of file:/C:/Users/vinnie/src/boost/bin.v2/libs/url/doc/_reference-dir/base-stage1.xsl  
Warning  
  XTDE0540: Ambiguous rule match for  
  /doxygen/compounddef[1]/sectiondef[1]/memberdef[1]/d:referenced-typedef-class[1]/compounddef[1]/detaileddescription[1]  
Matches both "element(Q{http://github.com/vinniefalco/docca}referenced-typedef-class)/element(Q{}compounddef)/element(Q{}detaileddescription)" on line 461 of file:/C:/Users/vinnie/src/boost/bin.v2/libs/url/doc/_reference-dir/base-stage1.xsl  
and "element(Q{}detaileddescription)[not(normalize-space(convertUntyped(data(.))))]" on line 216 of file:/C:/Users/vinnie/src/boost/bin.v2/libs/url/doc/_reference-dir/base-stage1.xsl  
Warning  
  XTDE0540: Ambiguous rule match for  
  /doxygen/compounddef[1]/sectiondef[1]/memberdef[1]/d:referenced-typedef-class[1]/compounddef[1]/detaileddescription[1]  
Matches both "element(Q{http://github.com/vinniefalco/docca}referenced-typedef-class)/element(Q{}compounddef)/element(Q{}detaileddescription)" on line 461 of file:/C:/Users/vinnie/src/boost/bin.v2/libs/url/doc/_reference-dir/base-stage1.xsl  
and "element(Q{}detaileddescription)[not(normalize-space(convertUntyped(data(.))))]" on line 216 of file:/C:/Users/vinnie/src/boost/bin.v2/libs/url/doc/_reference-dir/base-stage1.xsl  
Warning  
  XTDE0540: Ambiguous rule match for  
  /doxygen/compounddef[1]/sectiondef[1]/memberdef[1]/d:referenced-typedef-class[1]/compounddef[1]/detaileddescription[1]  
Matches both "element(Q{http://github.com/vinniefalco/docca}referenced-typedef-class)/element(Q{}compounddef)/element(Q{}detaileddescription)" on line 461 of file:/C:/Users/vinnie/src/boost/bin.v2/libs/url/doc/_reference-dir/base-stage1.xsl  
and "element(Q{}detaileddescription)[not(normalize-space(convertUntyped(data(.))))]" on line 216 of file:/C:/Users/vinnie/src/boost/bin.v2/libs/url/doc/_reference-dir/base-stage1.xsl  
Warning  
  XTDE0540: Ambiguous rule match for  
  /doxygen/compounddef[1]/sectiondef[1]/memberdef[1]/d:referenced-typedef-class[1]/compounddef[1]/detaileddescription[1]  
Matches both "element(Q{http://github.com/vinniefalco/docca}referenced-typedef-class)/element(Q{}compounddef)/element(Q{}detaileddescription)" on line 461 of file:/C:/Users/vinnie/src/boost/bin.v2/libs/url/doc/_reference-dir/base-stage1.xsl  
and "element(Q{}detaileddescription)[not(normalize-space(convertUntyped(data(.))))]" on line 216 of file:/C:/Users/vinnie/src/boost/bin.v2/libs/url/doc/_reference-dir/base-stage1.xsl  
Warning  
  XTDE0540: Ambiguous rule match for  
  /doxygen/compounddef[1]/sectiondef[1]/memberdef[1]/d:referenced-typedef-class[1]/compounddef[1]/detaileddescription[1]  
Matches both "element(Q{http://github.com/vinniefalco/docca}referenced-typedef-class)/element(Q{}compounddef)/element(Q{}detaileddescription)" on line 461 of file:/C:/Users/vinnie/src/boost/bin.v2/libs/url/doc/_reference-dir/base-stage1.xsl  
and "element(Q{}detaileddescription)[not(normalize-space(convertUntyped(data(.))))]" on line 216 of file:/C:/Users/vinnie/src/boost/bin.v2/libs/url/doc/_reference-dir/base-stage1.xsl  
Warning  
  XTDE0540: Ambiguous rule match for  
  /doxygen/compounddef[1]/sectiondef[1]/memberdef[1]/d:referenced-typedef-class[1]/compounddef[1]/detaileddescription[1]  
Matches both "element(Q{http://github.com/vinniefalco/docca}referenced-typedef-class)/element(Q{}compounddef)/element(Q{}detaileddescription)" on line 461 of file:/C:/Users/vinnie/src/boost/bin.v2/libs/url/doc/_reference-dir/base-stage1.xsl  
and "element(Q{}detaileddescription)[not(normalize-space(convertUntyped(data(.))))]" on line 216 of file:/C:/Users/vinnie/src/boost/bin.v2/libs/url/doc/_reference-dir/base-stage1.xsl  
Warning  
  XTDE0540: Ambiguous rule match for  
  /doxygen/compounddef[1]/sectiondef[1]/memberdef[1]/d:referenced-typedef-class[1]/compounddef[1]/detaileddescription[1]  
Matches both "element(Q{http://github.com/vinniefalco/docca}referenced-typedef-class)/element(Q{}compounddef)/element(Q{}detaileddescription)" on line 461 of file:/C:/Users/vinnie/src/boost/bin.v2/libs/url/doc/_reference-dir/base-stage1.xsl  
and "element(Q{}detaileddescription)[not(normalize-space(convertUntyped(data(.))))]" on line 216 of file:/C:/Users/vinnie/src/boost/bin.v2/libs/url/doc/_reference-dir/base-stage1.xsl  
```  
  
The docs still generate but the warnings seem troubling.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-10-10 00:59:09 UTC  
> Url: https://github.com/boostorg/docca/issues/99#issuecomment-939384708  

I reverted the change for now

---

## Comment 2

> Username: evanlenz  
> Created at: 2021-10-10 03:02:45 UTC  
> Url: https://github.com/boostorg/docca/issues/99#issuecomment-939397175  

That'll teach me to run the full test script I created every time and not just the example build! I'll get this fixed shortly. Thanks for reporting and sorry for the trouble!

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-10-10 03:15:33 UTC  
> Url: https://github.com/boostorg/docca/issues/99#issuecomment-939398389  

No trouble, and it did produce the correct output so... I probably should have paid more attention to the log before merging :)

---

## Comment 4

> Username: evanlenz  
> Created at: 2021-10-10 03:38:39 UTC  
> Url: https://github.com/boostorg/docca/issues/99#issuecomment-939400128  

Just if you're curious, the [default priority](https://lenzconsulting.com/how-xslt-works/#priority) for the new rule and an existing rule was the same (.5). Some `<detaileddescription>` elements (but none in the example project) matched both rules. The fallback is to use the last rule in document order (line 461), which Saxon did, along with emitting a warning. It just so happened that it didn't make any difference to the output in this case. But it certainly didn't represent my intention, and the warning would have alerted me to the problem. I'll be sure to run all the builds from now on. Thanks again for reporting!

---

## Comment 5

> Username: vinniefalco  
> Created at: 2021-10-10 03:44:38 UTC  
> Url: https://github.com/boostorg/docca/issues/99#issuecomment-939400586  

.5? is that a fraction?

---

## Comment 6

> Username: evanlenz  
> Created at: 2021-10-10 04:06:59 UTC  
> Url: https://github.com/boostorg/docca/issues/99#issuecomment-939402545  

Yep, the default priorities in XSLT 3.0 are -0.5, -0.25, 0, 0.25, and 0.5. Weird, huh? Default priority is analogous to the specificity of CSS rules, except that in XSLT you just have that small set of possible default priorities (based on the syntax of the `match` pattern), whereas CSS has a weighting algorithm based on the number and type of selectors. In XSLT, I typically only need to use `priority="1"` (as in the above fix), just to make the priority higher than 0.5. If there are several otherwise-ambiguous rule matches, I might end up with `priority="3"`, `priority="2"`, and `priority="1"`, for example. The higher the number, the higher the priority.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2021-10-11 02:17:55 UTC  
> Url: https://github.com/boostorg/docca/issues/99#issuecomment-939623508  

The C++-head inside of me thinks "What, it uses floating point numbers instead of integers? That's a performance issue!" But then I realize that floating point calculations are the least of its worries...
