# #304 - Documentation omnibus [Open]

> Username: joaquintides  
> Created at: 2025-01-27 10:40:13 UTC  
> Updated at: 2025-01-27 10:40:20 UTC  
> Url: https://github.com/boostorg/unordered/issues/304  

The recent move to Antora for Unordered docs has left some minor glitches that we compile in this omnibus issue:  
  
**1. Reference sections have "Untitled ..." tab name.** See for instance [here](https://www.boost.org/doc/libs/develop/libs/unordered/doc/html/unordered/reference/unordered_flat_map.html):  
  
![Image](https://github.com/user-attachments/assets/061137b6-4443-4b0b-b810-52dc1e2d8c7d)  
  
This is due to the fact that reference pages have no main header (note the first subsection begins with `==`), which, in the context of Antora, results in the page having no title:  
  
```  
[#unordered_flat_map]  
== Class Template unordered_flat_map  
  
:idprefix: unordered_flat_map_  
  
`boost::unordered_flat_map` — An open-addressing unordered associative container[...]  
```  
  
There are two possible approaches to fix this:  
  
* Somehow assign a title to the page that serves to compose the tab name while still not displaying on the page proper. I've experimented with attributes `:notitle:`, `:showtitle!:` etc., to no avail. See [this conversation](https://antora.zulipchat.com/#narrow/channel/282400-users/topic/Hiding.20title.20of.20a.20page) on Antora's support forum.  
* Move all level _n_ sections to level _n_-1. This looks ugly, as the headers appear too big for my taste.  
  
**2. Mobile devices: font size too big in some parts of the reference.** See for instance [this](https://www.boost.org/doc/libs/develop/libs/unordered/doc/html/unordered/reference/unordered_flat_map.html#unordered_flat_map_try_emplace) as it appears on my iPhone 8:  
  
<img src="https://github.com/user-attachments/assets/41b5f4f7-10c9-40bf-b7db-a2ec13019d5e" width=300>  
<img src="https://github.com/user-attachments/assets/4621138c-e0cc-4ca1-ac23-bf0537c11d6f" width=300>  
  
This seems to happen when inserting a block of code inside a "Notes:" construct:  
https://github.com/boostorg/unordered/blob/3d2c63f744809cdb4d41047329fc9c4893b6cc8d/doc/modules/ROOT/pages/reference/unordered_flat_map.adoc?plain=1#L913-L940  
  
**3. Mobile devices: two clicks needed to go to a TOC section.** Tested on my iPhone 8: go to the TOC and click on any entry (in the images below, "Hash Quality") --> the entry becomes underlined and a further click is needed to go through it:  
  
<img src="https://github.com/user-attachments/assets/940dcd18-33e6-45b0-9acf-8e8297d821bd" width=300>  
<img src="https://github.com/user-attachments/assets/fbc7a624-a541-42b4-a49c-057e636f4f91" width=300>
