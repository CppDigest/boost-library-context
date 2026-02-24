# #9 Fix dtor 404 errors on github.io: [Closed]

> Username: ximinez  
> Created at: 2016-10-19 20:58:33 UTC  
> Updated at: 2016-12-19 23:32:37 UTC  
> Closed at: 2016-12-19 23:32:09 UTC  
> Url: https://github.com/boostorg/docca/pull/9  

- Relevant github docs: https://help.github.com/articles/files-that-start-with-an-underscore-are-missing/  
- Translate ID "~Label" to "Label_dtor_" instead of "_Label"  
- Translate any IDs starting with "_" to start with "000_". This makes a  
  valid URL, but not a valid C identifier.

---

## Comment 1

> Username: ximinez  
> Created_at: 2016-10-19 21:43:04 UTC  
> Url: https://github.com/boostorg/docca/pull/9#issuecomment-254949736  

Example rippled generated documentation with this change: https://ximinez.github.io/dtor/  
Specifically: https://ximinez.github.io/dtor/rippled/ref/ripple__STObject/STObject_dtor_.html

---

## Comment 2

> Username: ximinez  
> Created_at: 2016-10-25 22:35:47 UTC  
> Updated_at: 2016-10-25 22:37:00 UTC  
> Url: https://github.com/boostorg/docca/pull/9#issuecomment-256198040  

Rewrote history again.  
  
1) Any changes to the "id" of a class member with a leading underscore breaks the link from within the class (though the member page is still generated, appears in index and navigation buttons), so I removed that. Issue #10.  
  
2) '+', '-', and '*' were replaced, but '/' was not, so I added that.  
  
3) Updated `example` to add (I think) all of the cases described by `make_id`. [Example Preview Documentation](https://ximinez.github.io/docca_example/) - Note this example includes the `.nojekyll` workaround, so the links with leading underscores don't get 404s, eg. `class_type::_prot_enum`.

---
