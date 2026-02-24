# #63 - Markup in tables is ignored [Closed]

> Username: vinniefalco  
> Created at: 2021-03-04 16:42:48 UTC  
> Updated at: 2021-05-10 17:26:47 UTC  
> Closed at: 2021-05-10 17:26:47 UTC  
> Url: https://github.com/boostorg/docca/issues/63  

See the @ commands in the table, e.g. `@see` and `@li`:  
  
https://www.boost.org/doc/libs/1_75_0/libs/beast/doc/html/beast/ref/boost__beast__http__error.html

---

## Comment 1

> Username: evanlenz  
> Created at: 2021-03-04 20:56:34 UTC  
> Url: https://github.com/boostorg/docca/issues/63#issuecomment-790935711  

Markup in other tables is supported. This looks like it's specific to enums (maybe). In any case, Doxygen is failing to recognize the markup. This problem is apparently not unique to the XML output. It happens in the HTML output as well:  
  
![image](https://user-images.githubusercontent.com/604974/110027713-2b427f80-7ce7-11eb-8012-7e073d6696dd.png)  
  
The HTML output is consistent with the XML output, in that they both show that Doxygen is treating the description like a code block. The HTML above shows the fixed-width font and the XML output uses a `<verbatim>` tag:  
  
```  
          <detaileddescription>  
<para><verbatim>bar description  
  
@li First bullet referencing @ref issue_55  
  
@li Second bullet  
</verbatim> </para>  
          </detaileddescription>  
```  
  
I experimented with removing the indentation from the C++ code ([based on what I read here](https://www.doxygen.nl/manual/markdown.html#md_codeblock)) and got a better HTML result:  
  
![image](https://user-images.githubusercontent.com/604974/110028593-48c41900-7ce8-11eb-8e9c-5a6024e64517.png)  
  
It looks like the XML output gives us what we need too:  
  
```  
      <memberdef kind="enum" id="namespaceexample_1a92952c411ef6476c63739ae957a34f74" prot="public" static="no" strong="yes">  
        <type></type>  
        <name>issue_63</name>  
        <enumvalue id="namespaceexample_1a92952c411ef6476c63739ae957a34f74aacbd18db4cc2f85cedef654fccc4a4d8" prot="public">  
          <name>foo</name>  
          <briefdescription>  
<para>foo brief </para>  
          </briefdescription>  
          <detaileddescription>  
<para>foo description</para>  
<para><simplesect kind="see"><para><ref refid="structexample_1_1issue__55" kindref="compound">issue_55</ref> </para>  
</simplesect>  
</para>  
          </detaileddescription>  
        </enumvalue>  
        <enumvalue id="namespaceexample_1a92952c411ef6476c63739ae957a34f74a37b51d194a7513e45b56f6524f2d51f2" prot="public">  
          <name>bar</name>  
          <briefdescription>  
<para>bar brief </para>  
          </briefdescription>  
          <detaileddescription>  
<para>bar description</para>  
<para><itemizedlist>  
<listitem><para>First bullet referencing <ref refid="structexample_1_1issue__55" kindref="compound">issue_55</ref></para>  
</listitem>  
</itemizedlist>  
<itemizedlist>  
<listitem><para>Second bullet </para>  
</listitem>  
</itemizedlist>  
</para>  
          </detaileddescription>  
        </enumvalue>  
```  
  
But it doesn't look fully correct yet in docca's HTML output:  
  
![image](https://user-images.githubusercontent.com/604974/110028788-8d4fb480-7ce8-11eb-948f-3a25946237a1.png)  
  
Long story short:  
  
* This is a work in progress.  
* Changes to the C++ source formatting will probably be needed.  
* Quickbook support of this nested content needs to be verified and the XSLT updated accordingly.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-03-04 21:51:29 UTC  
> Url: https://github.com/boostorg/docca/issues/63#issuecomment-790970854  

awwww man that suxxxx

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-03-04 21:51:58 UTC  
> Url: https://github.com/boostorg/docca/issues/63#issuecomment-790971134  

Check the Doxygen changelogs / release notes to see if it is either fixed, or recognized as an open issue?

---

## Comment 4

> Username: evanlenz  
> Created at: 2021-03-09 08:36:19 UTC  
> Url: https://github.com/boostorg/docca/issues/63#issuecomment-793550250  

I couldn't tell for sure from the changelogs, so I downloaded the Doxygen 1.9.1 executable. Unfortunately, it still produces the same behavior in the HTML and XML. If I remove just one space from the indent, then it produces the desired XML and HTML result.  
  
For example, this:  
```  
    /** Additional buffers are required.  
  
        This error is returned under the following conditions:  
  
        @li During serialization when using @ref buffer_body.  
        The caller should update the body to point to a new  
        buffer or indicate that there are no more octets in  
        the body.  
  
        @li During parsing when using @ref buffer_body.  
        The caller should update the body to point to a new  
        storage area to receive additional body octets.  
    */  
    need_buffer,  
```  
  
Produces this:  
![image](https://user-images.githubusercontent.com/604974/110440962-16ab1200-806e-11eb-9320-64a69b230332.png)  
  
  
But this (with one space removed from the indents):  
```  
    /**  
     * Additional buffers are required.  
  
       This error is returned under the following conditions:  
  
       @li During serialization when using @ref buffer_body.  
       The caller should update the body to point to a new  
       buffer or indicate that there are no more octets in  
       the body.  
  
       @li During parsing when using @ref buffer_body.  
       The caller should update the body to point to a new  
       storage area to receive additional body octets.  
    */  
    need_buffer,  
```  
  
Produces this (the desired result):  
![image](https://user-images.githubusercontent.com/604974/110441191-55d96300-806e-11eb-85ef-443c6bdb1cea.png)  
  
We may have to live with this until a new version of Doxygen fixes it.  
  
However, I can still try to update our XSLT so that it more gracefully handles the markup when it is available, e.g. using real bullets (not asterisks) in the table rendering:  
  
![image](https://user-images.githubusercontent.com/604974/110442047-4c9cc600-806f-11eb-9dd3-02717e7f04af.png)

---

## Comment 5

> Username: evanlenz  
> Created at: 2021-03-29 06:24:28 UTC  
> Updated at: 2021-03-29 06:25:21 UTC  
> Url: https://github.com/boostorg/docca/issues/63#issuecomment-809104764  

The XSLT to Quickbook rendering is now improved:  
  
![image](https://user-images.githubusercontent.com/604974/112794661-1cb46300-901c-11eb-926f-9ef68740da23.png)  
  
The caveat is that the C++ comment still needs manipulation to avoid the interpretation as verbatim (code). This can be done, for example, by removing one space from the indent as in the test case:  
  
https://github.com/boostorg/docca/pull/64/commits/9d3644a0b239779e6725dbd66c7cf632af53deb3#diff-29ef6fd89a4b65fcbe08b2f12ca85cbcb33c5f81e8e78859681fc8fdddbe62c6R19-R25

---

## Comment 6

> Username: vinniefalco  
> Created at: 2021-03-30 17:16:07 UTC  
> Url: https://github.com/boostorg/docca/issues/63#issuecomment-810434863  

Hmm nice workaround :)
