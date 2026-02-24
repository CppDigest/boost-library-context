# #611 refactor documentation typo [Merged]

> Username: alandefreitas  
> Created at: 2022-10-25 20:07:14 UTC  
> Updated at: 2022-12-22 16:14:39 UTC  
> Merged at: 2022-10-26 21:17:09 UTC  
> Closed at: 2022-10-26 21:17:09 UTC  
> Url: https://github.com/boostorg/url/pull/611  

fix #606

---

## Comment 1

> Username: alandefreitas  
> Created_at: 2022-10-25 20:13:32 UTC  
> Url: https://github.com/boostorg/url/pull/611#issuecomment-1291083966  

I followed the exact instructions in `.drone/drone.sh` to install docca in clear VMs. Ubuntu 20 generates `operator__star_` and Ubuntu 22 generates `operator_star_` for some reason.   
  
The instructions use the exact same binaries for doxygen and saxonhe. So my guess is the versions of `xsltproc` that come with these installations are generating different outputs.

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-10-25 20:17:50 UTC  
> Url: https://github.com/boostorg/url/pull/611#issuecomment-1291088335  

An automated preview of the documentation is available at [https://611.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://611.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-10-25 20:19:29 UTC  
> Url: https://github.com/boostorg/url/pull/611#issuecomment-1291090073  

GCOVR code coverage report [https://611.url.prtest.cppalliance.org/gcovr/index.html](https://611.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://611.url.prtest.cppalliance.org/genhtml/index.html](https://611.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2022-10-26 00:32:44 UTC  
> Url: https://github.com/boostorg/url/pull/611#issuecomment-1291281417  

An automated preview of the documentation is available at [https://611.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://611.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2022-10-26 00:33:53 UTC  
> Url: https://github.com/boostorg/url/pull/611#issuecomment-1291282055  

GCOVR code coverage report [https://611.url.prtest.cppalliance.org/gcovr/index.html](https://611.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://611.url.prtest.cppalliance.org/genhtml/index.html](https://611.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2022-10-26 16:10:19 UTC  
> Url: https://github.com/boostorg/url/pull/611#issuecomment-1292281431  

Evan will look into this. But generally speaking, when we test with xsltproc they all need to be using the same version as what will be used to build the Boost release docs.

---

## Comment 7

> Username: evanlenz  
> Created_at: 2022-10-26 19:58:57 UTC  
> Updated_at: 2022-10-26 19:59:14 UTC  
> Url: https://github.com/boostorg/url/pull/611#issuecomment-1292587659  

@alandefreitas Can you look at the contents of _classboost_1_1urls_1_1pct__string__view.xml_ (output from Doxygen)? It should be in _bin.v2/libs/url/doc/\_reference-dir_.  
  
The reason I ask is that it appears that the output of Doxygen is what will dictate whether you get one or two underscores in that page ID. I am getting the same behavior as Vinnie (two underscores), resulting from the following in that file:  
  
```  
      <memberdef kind="function" id="classboost_1_1urls_1_1pct__string__view_1ad625af7f8285fe3d9150a715111d86c3" prot="public" static="no" const="yes" explicit="no" inline="yes" virt="non-virtual">  
        <type><ref refid="classboost_1_1urls_1_1decode__view" kindref="compound">decode_view</ref></type>  
        <definition>decode_view boost::urls::pct_string_view::operator *</definition>  
        <argsstring>() const noexcept</argsstring>  
        <name>operator *</name>  
        <!-- ...snip... -->  
      </memberdef>  
```  
  
Notice the space in `operator *`. My only hypothesis so far is that your file has `<name>operator*</name>`. Can you see if this might be the issue and then we can go from there? Thanks!

---

## Comment 8

> Username: alandefreitas  
> Created_at: 2022-10-26 20:03:28 UTC  
> Url: https://github.com/boostorg/url/pull/611#issuecomment-1292591939  

> Evan will look into this. But generally speaking, when we test with xsltproc they all need to be using the same version as what will be used to build the Boost release docs.  
  
Yes. I agree.  
  
I tried to investigate this further by updating or downgrading `xsltproc`. This is the `xsltproc --version` output:  
  
Ubuntu 20:  
  
```console  
Using libxml 20910, libxslt 10134 and libexslt 820  
xsltproc was compiled against libxml 20910, libxslt 10134 and libexslt 820  
libxslt 10134 was compiled against libxml 20910  
libexslt 820 was compiled against libxml 20910  
```  
  
Ubuntu 22:  
  
```console  
Using libxml 20913, libxslt 10134 and libexslt 820  
xsltproc was compiled against libxml 20913, libxslt 10134 and libexslt 820  
libxslt 10134 was compiled against libxml 20913  
libexslt 820 was compiled against libxml 20913  
```  
  
The message is not very straightforward and I couldn't find a way to upgrade/downgrade it. I think it's just not something people do very often. But I'll have a better look.  
  
Assuming `xsltproc` is really the problem, this problem is going to become more and more common as people and the VMs in CI update Ubuntu.

---

## Comment 9

> Username: evanlenz  
> Created_at: 2022-10-26 20:07:31 UTC  
> Url: https://github.com/boostorg/url/pull/611#issuecomment-1292595757  

If my hypothesis is correct, then xsltproc is a complete red herring, because the difference occurs far upstream. But we'll see, based on what you find in that file!

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2022-10-26 20:13:52 UTC  
> Url: https://github.com/boostorg/url/pull/611#issuecomment-1292601939  

GCOVR code coverage report [https://611.url.prtest.cppalliance.org/gcovr/index.html](https://611.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://611.url.prtest.cppalliance.org/genhtml/index.html](https://611.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2022-10-26 20:16:59 UTC  
> Url: https://github.com/boostorg/url/pull/611#issuecomment-1292604755  

An automated preview of the documentation is available at [https://611.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://611.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 12

> Username: alandefreitas  
> Created_at: 2022-10-26 20:32:07 UTC  
> Url: https://github.com/boostorg/url/pull/611#issuecomment-1292620245  

> it appears that the output of Doxygen is what will dictate whether you get one or two underscores  
  
I think you are right. I believe Ubuntu 22 comes with doxygen 1.9.1 (`sudo apt install -y doxygen`) and `b2` wasn't picking up the `/usr/local/` version installed from `https://github.com/doxygen/doxygen.git#Release_1_8_15`. I get different results when I purge doxygen  1.9.1.   
  
<details>  
  <summary>Ubuntu 22 + doxygen 1.9.1 (sudo apt install -y doxygen)</summary>  
  
```console  
$ doxygen --version  
1.9.1  
```  
  
```xml  
<memberdef kind="function" id="classboost_1_1urls_1_1pct__string__view_1a8f4e46a6c00359c82fd29b601c556191" prot="public" static="no" const="yes" explicit="no" inline="yes" noexcept="yes" virt="non-virtual">  
        <type><ref refid="classboost_1_1urls_1_1decode__view" kindref="compound">decode_view</ref></type>  
        <definition>decode_view boost::urls::pct_string_view::operator*</definition>  
        <argsstring>() const noexcept</argsstring>  
        <name>operator*</name>  
        <briefdescription>  
<para>Return the string as a range of decoded characters. </para>  
        </briefdescription>  
        <detaileddescription>  
<para><simplesect kind="par"><title>Complexity</title><para>Constant.</para>  
</simplesect>  
<simplesect kind="par"><title>Exception Safety</title><para>Throws nothing.</para>  
</simplesect>  
<simplesect kind="see"><para><ref refid="classboost_1_1urls_1_1decode__view" kindref="compound">decode_view</ref>. </para>  
</simplesect>  
</para>  
        </detaileddescription>  
        <inbodydescription>  
        </inbodydescription>  
        <location file="libs/url/include/boost/url/pct_string_view.hpp" line="305" column="5" bodyfile="libs/url/include/boost/url/decode_view.hpp" bodystart="539" bodyend="542"/>  
      </memberdef>  
```  
</details>  
  
<details>  
  <summary>Ubuntu 22 + doxygen 1.8.15 (https://github.com/doxygen/doxygen.git#Release_1_8_15)</summary>  
  
  
```console  
$ doxygen --version  
1.8.15  
```  
  
```xml  
<memberdef kind="function" id="classboost_1_1urls_1_1pct__string__view_1ad625af7f8285fe3d9150a715111d86c3" prot="public" static="no" const="yes" explicit="no" inline="yes" virt="non-virtual">  
        <type><ref refid="classboost_1_1urls_1_1decode__view" kindref="compound">decode_view</ref></type>  
        <definition>decode_view boost::urls::pct_string_view::operator *</definition>  
        <argsstring>() const noexcept</argsstring>  
        <name>operator *</name>  
        <briefdescription>  
<para>Return the string as a range of decoded characters. </para>  
        </briefdescription>  
        <detaileddescription>  
<para><simplesect kind="par"><title>Complexity</title><para>Constant.</para>  
</simplesect>  
<simplesect kind="par"><title>Exception Safety</title><para>Throws nothing.</para>  
</simplesect>  
<simplesect kind="see"><para><ref refid="classboost_1_1urls_1_1decode__view" kindref="compound">decode_view</ref>. </para>  
</simplesect>  
</para>  
        </detaileddescription>  
        <inbodydescription>  
        </inbodydescription>  
        <location file="libs/url/include/boost/url/pct_string_view.hpp" line="305" column="1" bodyfile="libs/url/include/boost/url/decode_view.hpp" bodystart="539" bodyend="542"/>  
      </memberdef>  
```  
  
</details>  
  
<details>  
  <summary>Ubuntu 20</summary>  
  
  
```console  
$ doxygen --version  
1.8.15  
```  
  
```xml  
<memberdef kind="function" id="classboost_1_1urls_1_1pct__string__view_1ad625af7f8285fe3d9150a715111d86c3" prot="public" static="no" const="yes" explicit="no" inline="yes" virt="non-virtual">  
<type>  
<ref refid="classboost_1_1urls_1_1decode__view" kindref="compound">decode_view</ref>  
</type>  
<definition>decode_view boost::urls::pct_string_view::operator *</definition>  
<argsstring>() const noexcept</argsstring>  
<name>operator *</name>  
<briefdescription>  
<para>Return the string as a range of decoded characters. </para>  
</briefdescription>  
<detaileddescription>  
<para>  
<simplesect kind="par">  
<title>Complexity</title>  
<para>Constant.</para>  
</simplesect>  
<simplesect kind="par">  
<title>Exception Safety</title>  
<para>Throws nothing.</para>  
</simplesect>  
<simplesect kind="see">  
<para>  
<ref refid="classboost_1_1urls_1_1decode__view" kindref="compound">decode_view</ref>  
.  
</para>  
</simplesect>  
</para>  
</detaileddescription>  
<inbodydescription> </inbodydescription>  
<location file="libs/url/include/boost/url/pct_string_view.hpp" line="305" column="1" bodyfile="libs/url/include/boost/url/decode_view.hpp" bodystart="539" bodyend="542"/>  
</memberdef>  
```  
  
</details>  
  
In a way, this is a similar problem as more and more people upgrade to doxygen 1.9.1.

---
