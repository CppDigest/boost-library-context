# #40 - Replace sed-based customization with XSLT import [Closed]

> Username: vinniefalco  
> Created at: 2019-12-29 16:52:51 UTC  
> Updated at: 2021-02-25 18:55:13 UTC  
> Closed at: 2021-02-25 18:55:13 UTC  
> Url: https://github.com/boostorg/docca/issues/40  

The "convenience-header" mode used for the customization is not used anywhere in docca. Thus, the convenience header feature is broken.

---

## Comment 1

> Username: evanlenz  
> Created at: 2020-12-30 22:19:13 UTC  
> Url: https://github.com/boostorg/docca/issues/40#issuecomment-752775508  

It looks like this is working now, no?  
  
Relatedly, I would like to refactor the customization mechanism, dispensing with the sed-based text replacement. This will simplify the Jam file some. Although I had already done this in the original project, it might not have been structured in a useful way for individual projects (like example and json). Basically, all we need to do is have the docca XSLT code include or import the custom files, e.g.:  
  
* json/doc/xsl/includes.xsl,  
* json/doc/xsl/config.xsl, and  
* json/doc/xsl/class_detail.xsl (although this is not currently used in docca—do we still need it?)  
  
The Jamfile, rather than re-implementing the sed-based text replacement for each project, would just be responsible for copying those files into the same folder as the docca XSLT. (I can rename docca/include/config.xsl to base-config.xsl so there are no file-naming conflicts.)  
  
We would accordingly update the Jamfile and xsl directory contents of libs/beast/doc, libs/json/doc, and tools/docca/example. (Are there any other projects using docca yet?)  
  
If all of this sounds amenable to you, we could track it in a new issue and close this one (or however you'd like to handle it).

---

## Comment 2

> Username: evanlenz  
> Created at: 2020-12-30 22:22:56 UTC  
> Url: https://github.com/boostorg/docca/issues/40#issuecomment-752776295  

Also, if you are hesitant to let me make this change, that's fine too. It requires some coordination between the projects so nothing is left in a broken state. I assume there's a way to do that using the git submodule mechanism, but I've never done it before. Let me know if you have any concerns. I could also demo it sometime for you too. Basically, the build would be a little less magical and the XSLT a little more straightforward.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-01-06 17:46:12 UTC  
> Url: https://github.com/boostorg/docca/issues/40#issuecomment-755453824  

Haven't forgotten about this, working on some other stuff at the moment but I will circle back

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-01-25 23:41:09 UTC  
> Url: https://github.com/boostorg/docca/issues/40#issuecomment-767183562  

Yes it would be better if the customization was done in XSL, because `sed` is not portable (the docs don't build on the Mac for example). No problem updating each project that uses docca. One possibility is we could add a version number to the repository's config.xsl file for compatibility so in the future we are not forced to update all the repositories at once?

---

## Comment 5

> Username: evanlenz  
> Created at: 2021-02-02 05:51:43 UTC  
> Url: https://github.com/boostorg/docca/issues/40#issuecomment-771382717  

That's a great point about compatibility. However, one of the nice things about xsl:import (which would replace the `sed`-based textual inclusion) is that we can still define default values for the variables in the docca code itself (which would get overridden by any custom values that are supplied in a project's config.xsl file). This means we can add new custom variables (as long as we supply a default) in docca, without having to immediately go and update every project that uses it. Those projects could define a custom value at their leisure. The upshot is that adding new customizable variables to docca won't break any existing projects.  
  
It's true that when initially changing the customization mechanism (to <xsl:import> from sed) that we'll need to do it for all existing projects all at once. But thankfully that should be a one-time thing.

---

## Comment 6

> Username: evanlenz  
> Created at: 2021-02-03 06:32:51 UTC  
> Url: https://github.com/boostorg/docca/issues/40#issuecomment-772270504  

@vinniefalco Another question I have is whether we need both config.xsl and includes.xsl (class_detail.xsl looks no longer use and can be deleted, I assume). My guess is that the reason we have more than one file is a historical artifact of the previous mechanism where it was important to be able to include the different code snippets in different parts of the including file. But now, we are using xsl:import of top-level elements, and their order and relative placement doesn't matter. For that reason, I propose that we simplify the mechanism so that each project has just one config.xsl file (consisting of the combined content of config.xsl and includes.xsl, but now as a valid XSLT module that can be imported).  
  
Does this sound fine to you? In other words, do you see any remaining reason why projects would need to have two separate config files (e.g. because one was to be shared by multiple projects and the other was unique to this one)? Even if we saw that need in the future, we could plan for it by making config.xsl the root of any number of modules that could get automatically copied by the Jamfile.  
  
Anyway, how does this sound? If your answer is, "I'm not sure but I trust you," that is fine too. :-)

---

## Comment 7

> Username: vinniefalco  
> Created at: 2021-02-03 10:17:58 UTC  
> Url: https://github.com/boostorg/docca/issues/40#issuecomment-772397022  

One file is better than two or more files

---

## Comment 8

> Username: evanlenz  
> Created at: 2021-02-03 17:21:56 UTC  
> Url: https://github.com/boostorg/docca/issues/40#issuecomment-772679364  

This now seems like a good issue for tracking the change, but the title is obsolete now and it looks like I can't edit it. It would be nice if it said something like "Replace sed-based customization with XSLT import" or "Modernize customization method" (or whatever). I'll proceed regardless.
