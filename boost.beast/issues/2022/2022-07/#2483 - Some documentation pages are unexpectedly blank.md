# #2483 - Some documentation pages are unexpectedly blank [Open]

> Username: inetknght  
> Created at: 2022-07-13 15:02:42 UTC  
> Updated at: 2024-01-04 08:21:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2483  

### Version of Beast  
  
it seems all versions  
  
### Steps necessary to reproduce the problem  
  
Small documentation issue.  
  
Main documentation page [[master](https://www.boost.org/doc/libs/master/libs/beast/doc/html/index.html), [1.78.0](https://www.boost.org/doc/libs/1_78_0/libs/beast/doc/html/index.html), [1.68.0](https://www.boost.org/doc/libs/1_68_0/libs/beast/doc/html/index.html)] -> see "HTTP Examples" or "More Examples" with subitems. Click it.  
HTTP Examples documentation page [[master](https://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/more_examples.html), [1.78.0](https://www.boost.org/doc/libs/1_78_0/libs/beast/doc/html/beast/more_examples.html), [1.68.0](https://www.boost.org/doc/libs/1_68_0/libs/beast/doc/html/beast/more_examples.html)] -> empty page. I would expect the subitems on the previous page to be listed here  
  
I did a small amount of digging. It looks like HTTP Examples page is generated from [this QBK](https://github.com/boostorg/beast/blob/b7344b0d501f23f763a76488826dde8c31f34b5d/doc/qbk/05_http_examples/_http_examples.qbk)? I see the text `These examples in this section are working functions that may be found in the examples directory. They demonstrate the usage of the library for a variety of scenarios.` is there and additional text describing each sub item. But I am not familiar with QBK to diagnose why the page is empty.  
  
It seems some other sections are similarly empty. The Concepts [1.78.0](https://www.boost.org/doc/libs/1_78_0/libs/beast/doc/html/beast/concepts.html) page is similarly empty as is the [1.78.0 Configuration](https://www.boost.org/doc/libs/1_78_0/libs/beast/doc/html/beast/config.html) page. The [1.78.0 Quick Look](https://www.boost.org/doc/libs/1_78_0/libs/beast/doc/html/beast/quick_start.html) page is empty -- but it at least links to the `example` directory listing even if it doesn't describe which examples do what.  
  
Other sections appear to be correctly filled out. For example, the [1.78.0 WebSockets](https://github.com/boostorg/beast/blob/b7344b0d501f23f763a76488826dde8c31f34b5d/doc/qbk/06_websocket/_websocket.qbk) section has content.  
  
It looks like the WebSocket section QBK mentions only a single section (with notes, headings, tables, includes, etc) and links to other sections while the HTTP Examples QBK has multiple sections.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-07-13 15:06:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2483#issuecomment-1183343542  

The page is empty because its a new section I guess, press the "Next" button on that mostly blank page (the right arrow)

---

## Comment 2

> Username: sehe  
> Created at: 2022-07-13 16:11:42 UTC  
> Updated at: 2022-07-13 16:11:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2483#issuecomment-1183415890  

The settings for section chunking with HTML output are in `doc/Jamfile`:  
  
```  
  <xsl:param>chunk.section.depth=8                # Depth to which sections should be chunked  
  <xsl:param>chunk.first.sections=1               # Chunk the first top-level section?  
  <xsl:param>toc.section.depth=8                  # How deep should recursive sections appear in the TOC?  
  <xsl:param>toc.max.depth=8                      # How many levels should be created for each TOC?  
  <xsl:param>generate.section.toc.level=8         # Control depth of TOC generation in sections  
```  
See also https://boostorg.github.io/quickbook/doc/html/boost_doc_tools/parameters.html  
  
Changing to `chunk.first.sections=0` is too invasive (and would e.g. lead to the quick reference appearing on the same page as the TOC).  
  
Chunking (splitting to separate HTML pages) can be disabled locally:   
  
```  
[block'''<?dbhtml stop-chunking?>''']  
```  
  
Indeed including that leads to the first example appearing on the "more_examples.htm" page, while subsequent sections still appear on their own individual pages. I'd say we want to do this change:  
  
![image](https://user-images.githubusercontent.com/324097/178781123-c0c1b735-b52c-42ab-9767-decb3d1473b7.png)

---

## Comment 3

> Username: sehe  
> Created at: 2022-07-13 16:23:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2483#issuecomment-1183428094  

Mmm. Sadness:  
  
 1. I was potentially too quick. Apparently the behaviour I described was only at partial rendered documentation, after completing the local rendering, all the "More HTTP Examples" appeared on a single page, which is not what we wanted  
 2. The "Quick Start" (1_quick_look.qbk) appeared to have this `stop-chunking` directive but with a typo. "Fixing" the typo sadly shows the same behaviour of squashing all descendant sections to the single HMTL:  
![_backup_cloudbackup_custom_ex_superboost_libs_beast_doc_html_beast_quick_start html(iPad Air)](https://user-images.githubusercontent.com/324097/178783115-6408cfd8-44fc-40aa-8f24-c892f663a853.png)  
  
The best I think we could do, then, is manually create some placeholder context on the main section page, perhaps with a bespoke hyperlink that duplicates what the "Next" button does in context...

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-07-13 16:39:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2483#issuecomment-1183444350  

Yeah its not perfect..

---

## Comment 5

> Username: alandefreitas  
> Created at: 2022-07-13 20:01:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2483#issuecomment-1183623516  

We tried to fix that before. I don't even remember in what library.  
  
IIRC, the problem is we can't control the TOC depth for individual sections and the reference has subsections that look bad in the TOC.   
  
If that's the case, the best solution would be to adapt docca to generate a document hierarchy that looks betters in the TOC.

---

## Comment 6

> Username: sehe  
> Created at: 2022-07-13 20:39:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2483#issuecomment-1183655629  

I reckon it would be nice if we could include "local" TOCs on page currently empty-ish  
  
On Wed, Jul 13, 2022, at 10:01 PM, Alan de Freitas wrote:  
>   
>   
> We tried to fix that before. I don't even remember in what library.  
>   
> IIRC, the problem is we can't control the TOC depth for individual sections and the reference has subsections that look bad in the TOC.  
>   
> If that's the case, the best solution would be to adapt docca to generate a document hierarchy that looks betters in the TOC.  
>   
>   
> —  
> Reply to this email directly, view it on GitHub <https://github.com/boostorg/beast/issues/2483#issuecomment-1183623516>, or unsubscribe <https://github.com/notifications/unsubscribe-auth/AACPEAIVVMX6E7ELY6EG7L3VT4OATANCNFSM53PBWVXQ>.  
> You are receiving this because you commented.Message ID: ***@***.***>  
>

---

## Comment 7

> Username: inetknght  
> Created at: 2022-07-14 16:35:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2483#issuecomment-1184654913  

Yes, I think it would be good enough to have a local TOC with the subitems from the parent page and a link back to the parent page.  
  
As-is, if I copy the link from the main page and send it to a colleage, then the colleage could be lost because there's _no information_ on the page linked and navigation by icons in the top right isn't clear (no text) and, when they opened the direct link, they can't just press the browser's back button to work from the parent page. So having the local TOC would help to navigate around the documentation.

---

## Comment 8

> Username: alandefreitas  
> Created at: 2022-07-15 04:22:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2483#issuecomment-1185159042  

Just as a reference, this is what happens when we remove the blank page:   
  
![image](https://user-images.githubusercontent.com/5369819/179149763-dc8ed08f-f334-4554-9c7b-23d2cb76c55a.png)
