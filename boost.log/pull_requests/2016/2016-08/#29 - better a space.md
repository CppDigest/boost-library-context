# #29 better a space [Closed]

> Username: wasphin  
> Created at: 2016-08-19 03:55:27 UTC  
> Updated at: 2016-08-19 10:37:53 UTC  
> Closed at: 2016-08-19 10:37:53 UTC  
> Url: https://github.com/boostorg/log/pull/29  



---

## Comment 1

> Username: Lastique  
> Created_at: 2016-08-19 08:27:00 UTC  
> Url: https://github.com/boostorg/log/pull/29#issuecomment-240959033  

Could you clarify what this change does and why it is needed?

---

## Comment 2

> Username: wasphin  
> Created_at: 2016-08-19 09:01:48 UTC  
> Url: https://github.com/boostorg/log/pull/29#issuecomment-240966268  

At first I just find it will not be rendered properly in a browser, see https://github.com/Thiht/markdown-viewer/issues/70. Then I got https://github.com/markdown-it/markdown-it/issues/240 and https://github.com/Microsoft/vscode/issues/8376. At last I found some more, maybe formal? see https://daringfireball.net/projects/markdown/syntax#header and http://spec.commonmark.org/0.25/#atx-headings. And there is a not that official [markdown-style-guide](http://www.cirosantilli.com/markdown-style-guide/#headers).  
As a result, we'd better  
  
> Include a single space between the # and the text of the header.

---

## Comment 3

> Username: Lastique  
> Created_at: 2016-08-19 10:37:53 UTC  
> Url: https://github.com/boostorg/log/pull/29#issuecomment-240985774  

Thanks. Done in 2f80598.

---
