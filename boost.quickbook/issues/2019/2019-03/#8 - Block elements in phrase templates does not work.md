# #8 - Block elements in phrase templates does not work [Closed]

> Username: Kojoley  
> Created at: 2019-03-28 22:15:49 UTC  
> Updated at: 2019-03-28 23:10:27 UTC  
> Closed at: 2019-03-28 22:45:01 UTC  
> Url: https://github.com/boostorg/quickbook/issues/8  

```qbk  
[template foo[] [blurb Blah, blah, blah]]  
```  
(an example from https://www.boost.org/doc/libs/1_69_0/doc/html/quickbook/versions.html#language_versions.1_7.phrase_block_templates) renders as:  
```xml  
    <para>  
      [blurb Blah, blah, blah]  
    </para>  
```  
  
An example from https://www.boost.org/doc/libs/1_69_0/doc/html/quickbook/syntax/block.html#quickbook.syntax.block.block errors if typed as-is because of problems with nullary templates  
```  
foo.qbk:13: error: Invalid number of arguments passed. Expecting: 11 argument(s), got: 0 argument(s) instead.  
```  
  
but after fixing the error:  
  
```qbk  
[template chapterend[]  
[block'''</chapter>''']  
]  
```  
  
it renders as:  
  
```xml  
    <para>  
      [block<chapter><title>An example chapter</title>]  
    </para>  
    <para>  
      Content  
    </para>  
    <para>  
      [block</chapter>]  
    </para>  
```

---

## Comment 1

> Username: danieljames  
> Created at: 2019-03-28 22:38:09 UTC  
> Url: https://github.com/boostorg/quickbook/issues/8#issuecomment-477796599  

Can you give a complete example?

---

## Comment 2

> Username: Kojoley  
> Created at: 2019-03-28 22:44:59 UTC  
> Url: https://github.com/boostorg/quickbook/issues/8#issuecomment-477798197  

I am sorry for disturb here, turns out it was my fault. The problem was in `[quickbook 1.5]`. I did not know that there even was this thing before looking into quickbook tests, and docs does not say it was introduced in 1.7 and requires 1.7 mode enabled...

---

## Comment 3

> Username: danieljames  
> Created at: 2019-03-28 22:53:01 UTC  
> Url: https://github.com/boostorg/quickbook/issues/8#issuecomment-477800015  

The beginning of the page you linked to describes enabling 1.7, but reading it now, it's not very clear, as it seems to only be about upgrading an old document. It's also documented here:  
  
https://www.boost.org/doc/libs/1_69_0/doc/html/quickbook/syntax/structure.html#quickbook.syntax.structure.docinfo.attributes.quickbook_specific_meta_data  
  
But maybe it should be repeated more.

---

## Comment 4

> Username: Kojoley  
> Created at: 2019-03-28 22:59:04 UTC  
> Url: https://github.com/boostorg/quickbook/issues/8#issuecomment-477801372  

It seems that some people already fell into this trap, if you look across the projects there are definitions of that template, but it is not used and a version below 1.7 is used: https://github.com/boostorg/tuple/blob/0b724234cedad3302d47a5a432998bf42c3640ba/doc/tuple_users_guide.qbk#L10-L27 https://github.com/boostorg/beast/blob/develop/doc/qbk/main.qbk and many more.

---

## Comment 5

> Username: danieljames  
> Created at: 2019-03-28 23:10:27 UTC  
> Url: https://github.com/boostorg/quickbook/issues/8#issuecomment-477803870  

Are you sure they're wrong? As far as I can tell, they're okay as the tuple templates are block templates and the beast templates are phrase templates with phrase content, but it's some time since I've done this, so maybe I'm wrong. The change in 1.7 was trying to reduce the need to understand these subtle differences.
