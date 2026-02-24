# #11 - Generated section ids have a colon prefix [Closed]

> Username: Lastique  
> Created at: 2021-05-26 19:03:25 UTC  
> Updated at: 2022-09-26 15:14:50 UTC  
> Closed at: 2022-09-26 15:14:50 UTC  
> Url: https://github.com/boostorg/quickbook/issues/11  

This problem happens when I build [Boost.Log](https://github.com/boostorg/log/tree/develop/doc) docs locally with the following command line:  
  
```  
b2 -j8 release  
```  
  
The build process produces a lot of errors like this:  
  
```  
Error: no ID for constraint linkend: "log.design".  
```  
  
for various link ids mentioned in the QuickBook `[link]` tags, and the corresponding links in the final docs are broken.  
  
The problem seems to be because the `[link]` tags get translated to `<link>` BoostBook tags with a `linkend` attribute that matches the id specified in `[link]` QuickBook tag, but the actual `<section>` in BoostBook has `id` with a colon character prepended. For example:  
  
- In QuickBook there is [`[link log.design Design overview]`](https://github.com/boostorg/log/blob/df4bc60d056a7ff37a0ec9110ddbeb364bc5c435/doc/log.qbk#L145) and the section is started with [`[section:design Design overview]`](https://github.com/boostorg/log/blob/df4bc60d056a7ff37a0ec9110ddbeb364bc5c435/doc/design.qbk#L10) (which is included into the main document using [`[include:log design.qbk]`](https://github.com/boostorg/log/blob/df4bc60d056a7ff37a0ec9110ddbeb364bc5c435/doc/log.qbk#L301)).  
- In the generated `log_doc.xml`, the link looks like `<link linkend="log.design">Design overview</link>` and the section starts with `<section id=":log.design">` (note the leading colon in the id). Also, the section starts with a `<title><link linkend=":log.design">Design overview</link></title>`, where the `link` also has a leading colon.  
- The generated `log_doc.docbook` is similar to `log_doc.xml` wrt. ids used in `link` and `section`.  
- In the generated html documentation, the link looks like `<a class="link" href="../">` and points to the root directory of the documentation (i.e. `libs/log/doc/html` in Boost root). There are `log` and `_log` directories in `libs/log/doc/html`, and in the latter, the `design.html` file contains `<a name=":log.design">`.  
  
Here is an attached generated `log_doc.xml`:  
[log_doc.xml.gz](https://github.com/boostorg/quickbook/files/6549069/log_doc.xml.gz)  
  
This happens on Kubuntu 21.04 with its stock xsltproc and DocBook packages.

---

## Comment 1

> Username: Kojoley  
> Created at: 2022-09-23 23:38:07 UTC  
> Url: https://github.com/boostorg/quickbook/issues/11#issuecomment-1256798129  

Did you use GCC 9.2-12.1 to compile Quickbook? Could be a GCC bug https://github.com/boostorg/spirit/issues/737 which seems to be fixed in 12.2.

---

## Comment 2

> Username: Lastique  
> Created at: 2022-09-24 01:25:07 UTC  
> Url: https://github.com/boostorg/quickbook/issues/11#issuecomment-1256827300  

Yes, very much likely so. I can't remember what gcc version was in Kubuntu 21.04, but the problem has not fixed for me to this day (I'm currently on Kubuntu 22.04 with gcc 11.2).
