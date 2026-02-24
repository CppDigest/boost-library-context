# #10 - make-id can't change members with leading underscore [Open]

> Username: ximinez  
> Created at: 2016-10-25 22:26:16 UTC  
> Updated at: 2016-10-25 22:29:29 UTC  
> Url: https://github.com/boostorg/docca/issues/10  

Any changes to the "id" of a class member within `make_id` with a leading underscore breaks the link from within the class (though the member page is still generated, appears in index and navigation buttons).  
  
This appears to occur in the last step where the docbook is translated to html.

---

## Comment 1

> Username: ximinez  
> Created at: 2016-10-25 22:29:29 UTC  
> Url: https://github.com/boostorg/docca/issues/10#issuecomment-256196492  

Without translation of leading underscore, all of the html generates fine, but when pushed to github.io, those pages return a 404 because of https://help.github.com/articles/files-that-start-with-an-underscore-are-missing/. There is a workaround described at that link.
