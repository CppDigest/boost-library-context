# #79 Modernize Docs [Merged]

> Username: alandefreitas  
> Created at: 2021-10-08 22:56:25 UTC  
> Updated at: 2021-11-22 14:55:22 UTC  
> Merged at: 2021-11-16 13:36:34 UTC  
> Closed at: 2021-11-16 13:36:34 UTC  
> Url: https://github.com/boostorg/utility/pull/79  

Preview: https://alandefreitas.github.io/boost-utility-docs/  
Old docs for comparison: https://www.boost.org/doc/libs/1_77_0/libs/utility/  
  
- Rewrite all html sections with common qbk conventions  
- Include/remove missing/deprecated sections  
- Include overview section with all utilities  
- Create docca reference section  
- Use correct qbk macros throughout the docs  
- Redirect old links

---

## Review 1 [Commented]

> Username: glenfe  
> Created_at: 2021-10-09 01:14:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/utility/pull/79#pullrequestreview-775524392  

📁 Assignable.html

```diff
   1 |- <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
```

> Username: glenfe  
> Created_at: 2021-10-09 01:06:15 UTC  
> Updated_at: 2021-10-09 01:14:19 UTC  
> Url: https://github.com/boostorg/utility/pull/79#discussion_r725407892  

Don't worry about turning the existing HTML files into redirects, it's fine to remove them entirely. People would be linking to a specific Boost version documentation that would have this file, so it's fine for the new version to not have it.

> Username: Lastique  
> Created_at: 2021-10-09 08:21:08 UTC  
> Updated_at: 2021-10-09 08:21:31 UTC  
> Url: https://github.com/boostorg/utility/pull/79#discussion_r725454759  

> People would be linking to a specific Boost version documentation that would have this file, so it's fine for the new version to not have it.  
  
Not necessarily. I've been using version-neutral links in some Boost docs. Though I'm not sure I used links to Boost.Utility. Maybe I'm not alone.

> Username: alandefreitas  
> Created_at: 2021-10-10 03:11:58 UTC  
> Url: https://github.com/boostorg/utility/pull/79#discussion_r725568355  

The html files in the root directory are so old that I assumed that's the only reason they are still there.  
On the other hand, they were mostly documenting stuff that's very old and not even part of boost.utility anymore.  
I tried to be conservative on that but I doubt many people rely on these links anyway.

> Username: glenfe  
> Created_at: 2021-10-10 13:59:10 UTC  
> Updated_at: 2021-10-10 13:59:11 UTC  
> Url: https://github.com/boostorg/utility/pull/79#discussion_r725643855  

The redirect files pointing to facilities moved to other libraries should be retained: e.g. the `checked_delete.html` file should still have to `<meta http-equiv=refresh content="0; URL=../core/doc/html/core/checked_delete.html">`. You shouldn't change those to redirect to Utility's main page.  
  
If facilities no longer exist, there's no point redirecting to Utility's `index.html` - those can be removed.

> Username: alandefreitas  
> Created_at: 2021-10-12 00:24:24 UTC  
> Updated_at: 2021-10-12 00:24:25 UTC  
> Url: https://github.com/boostorg/utility/pull/79#discussion_r726673499  

> The redirect files pointing to facilities moved to other libraries should be retained  
  
Done: d37d863f9b646496553eae5a8e9318c944761a96  
  
> If facilities no longer exist, (...) those can be removed.  
  
Broken links don't redirect to Utility's `index.html` though: https://www.boost.org/doc/libs/1_77_0/libs/utility/broken_link.html  
They look rather ugly. Is it safe to assume there's consensus on that yet?

> Username: glenfe  
> Created_at: 2021-10-12 01:01:04 UTC  
> Url: https://github.com/boostorg/utility/pull/79#discussion_r726684737  

If someone is linking to `boost.org/libs/utility/something.html` and `something` as a Boost facility doesn't even exist anymore, then there's no value in that link going to `utility/index.html` so it's fine if it's broken.


📁 doc/qbk/01_overview.qbk

```diff
   1 |+ [/
   2 |+     Copyright (c) 2019 Vinnie Falco (vinnie.falco@gmail.com)
```

> Username: glenfe  
> Created_at: 2021-10-09 01:10:57 UTC  
> Updated_at: 2021-10-09 01:14:19 UTC  
> Url: https://github.com/boostorg/utility/pull/79#discussion_r725408539  

For any files that you've copied content from some now-deleted file, also copy the Copyright from those files.

> Username: alandefreitas  
> Created_at: 2021-10-10 03:22:58 UTC  
> Url: https://github.com/boostorg/utility/pull/79#discussion_r725569287  

This one is actually a new file. The copyright says https://github.com/cppalliance/json because I copied some template from boost/json and forgot to update it.

> Username: glenfe  
> Created_at: 2021-10-10 13:54:57 UTC  
> Url: https://github.com/boostorg/utility/pull/79#discussion_r725643283  

Any new content should be `Copyright 2021 Alan de Freitas`

> Username: alandefreitas  
> Created_at: 2021-10-12 00:26:09 UTC  
> Url: https://github.com/boostorg/utility/pull/79#discussion_r726674010  

> Any new content should be Copyright 2021 Alan de Freitas  
  
Done: bd39d563ed4d2fbae82d8d4ceb10af93ae0c4e3c  
  
Should I also include `Copyright 2021 Alan de Freitas` additionally in files that have been extended, changed, or transcribed in .qbk? Or leaving the original authors is fine?

> Username: glenfe  
> Created_at: 2021-10-12 00:54:07 UTC  
> Url: https://github.com/boostorg/utility/pull/79#discussion_r726682470  

Both are fine - up to you.

---

📁 doc/qbk/01_overview.qbk

```diff
  12 |+ [block'''<?dbhtml stop-chunking?>''']
  13 |+ 
  14 |+ [$images/logo.png]
```

> Username: glenfe  
> Created_at: 2021-10-09 01:12:07 UTC  
> Updated_at: 2021-10-09 01:14:19 UTC  
> Url: https://github.com/boostorg/utility/pull/79#discussion_r725408735  

Not necessary. All the HTML files shown will have the Boost logo on the green banner.

> Username: alandefreitas  
> Created_at: 2021-10-10 03:17:36 UTC  
> Url: https://github.com/boostorg/utility/pull/79#discussion_r725568822  

The thing is Boost.Utility had a different logo and I didn't know where to put it.   
  
Maybe we could remove the logo entirely but I thought the people who created it wouldn't like that.

> Username: glenfe  
> Created_at: 2021-10-10 13:50:59 UTC  
> Url: https://github.com/boostorg/utility/pull/79#discussion_r725642727  

We can remove the logo entirely. There's no need to show two of them - the one on the green banner is sufficient.

> Username: alandefreitas  
> Created_at: 2021-10-12 00:26:32 UTC  
> Updated_at: 2021-10-12 00:26:33 UTC  
> Url: https://github.com/boostorg/utility/pull/79#discussion_r726674102  

Done: dd95ce7f0bfc4faae9ad53c198d7ee11d74a98dc


---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2021-10-11 18:22:24 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-940303351  

An automated preview of the documentation is available at [https://79.utility.prtest.cppalliance.org/libs/utility/index.html](https://79.utility.prtest.cppalliance.org/libs/utility/index.html)

---

## Comment 3

> Username: sdarwin  
> Created_at: 2021-10-11 18:40:02 UTC  
> Updated_at: 2021-10-11 18:41:49 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-940354618  

@alandefreitas @vinniefalco   
  
Added documentation previews for the utility repo (refer to previous comment in thread).      
  
As a comparison, most cppalliance repos place all docs in the doc/html folder.  That's clear and consistent.    boostorg/utility generates some of the docs in the root folder, and others in doc/html.  They aren't formatted the same way.  Maybe there is a historical reason for this?  
  
It might be good to standardize on .html file extension, instead of having some files .htm and others .html.

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2021-10-11 22:25:24 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-940489473  

> Maybe there is a historical reason for this?  
  
Probably. @pdimov ?

---

## Comment 5

> Username: pdimov  
> Created_at: 2021-10-11 22:48:57 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-940499322  

No reason; Utility is just old and a collection of unorganized stuff by multiple authors.  
  
But I'm not sure why we would move things to .qbk instead of straight to .asciidoc.

---

## Comment 6

> Username: Lastique  
> Created_at: 2021-10-11 23:12:27 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-940509525  

Why are we moving docs to `doc/qbk` instead of `doc`? And also, why rename and move the existing QuickBook files? I also don't think naming the files after the document structure (is this the intention?) is a good idea. Those are separate components, with independent docs, maintained by different people.  
  
I'd prefer if we had the traditional layout, i.e. the docs and the Jamfile are in `doc`, and named accordingly. See [Boost.Core](https://github.com/boostorg/core/tree/develop/doc) for an example (which actually partly derived from Utility).  
  
Re QuickBook vs asciidoc, we already have QuickBook docs in Utility, and it better works with Doxygen and code snippets. But I'm not nailed to QuickBook if there are reasons to switch to asciidoc.

---

## Comment 7

> Username: pdimov  
> Created_at: 2021-10-11 23:15:28 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-940510886  

Yeah, I always forget about Doxygen. Asciidoctor has no problems with code snippets, but it doesn't support Doxygen XML.

---

## Comment 8

> Username: vinniefalco  
> Created_at: 2021-10-11 23:43:54 UTC  
> Updated_at: 2021-10-11 23:44:57 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-940521359  

> I'm not sure why we would move things to .qbk instead of straight to .asciidoc.  
  
There was no preference indicated, so Alan was given a free choice. I'm not sure why we would move things to .asciidoc over .qbk, Is there an existing policy? I don't very much like the asciidoc docs to be honest, I find them hard to use as reference material (mp11 in particular). I am used to the Asio style of reference which feels superior, especially that there are individual pages instead of one enormous page. But I guess this choice is up to the owners of Boost.Utility.   
  
In the future before embarking on wholesale changes to modernize documentation, lets get buy-in from the authors first so we don't squander efforts.  
  
I don't have an opinion on the physical layout of the qbk files, but for reference this is how I lay mine out:  
https://github.com/boostorg/json/tree/develop/doc/qbk

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2021-10-12 00:27:17 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-940557509  

An automated preview of the documentation is available at [https://79.utility.prtest.cppalliance.org/libs/utility/index.html](https://79.utility.prtest.cppalliance.org/libs/utility/index.html)

---

## Comment 10

> Username: alandefreitas  
> Created_at: 2021-10-12 00:31:56 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-940559326  

> Added documentation previews for the utility repo (refer to previous comment in thread).  
  
@sdarwin I think the preview in https://79.utility.prtest.cppalliance.org/libs/utility/index.html is a preview of the current utility repo, not the fork with the new documentation.  
  
> As a comparison, most cppalliance repos place all docs in the doc/html folder. That's clear and consistent. boostorg/utility generates some of the docs in the root folder, and others in doc/html. They aren't formatted the same way. Maybe there is a historical reason for this?   
  
> It might be good to standardize on .html file extension, instead of having some files .htm and others .html.  
  
We had a small discussion on that [above](https://github.com/boostorg/utility/pull/79#discussion_r725407892). I think the files in the root folder are redirects to ensure old links don't break. I tried to be conservative here, but I can remove them if there's enough consensus that we should.

---

## Comment 11

> Username: alandefreitas  
> Created_at: 2021-10-12 00:35:08 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-940560593  

> But I'm not sure why we would move things to .qbk instead of straight to .asciidoc.  
  
@pdimov I considered both options. But some files already used .qbk and that seemed better for creating the reference with docca, which didn't exist before.

---

## Comment 12

> Username: pdimov  
> Created_at: 2021-10-12 00:55:06 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-940566978  

What's the purpose of naming the qbk files `02_01_base_from_member.qbk` instead of just `base_from_member.qbk`? Apart from being unnecessary, this will also mean that a bunch of files will need to be renamed if a new file is inserted or the files are reordered for some reason.

---

## Comment 13

> Username: glenfe  
> Created_at: 2021-10-12 01:02:34 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-940569339  

> What's the purpose of naming the qbk files 02_01_base_from_member.qbk instead of just base_from_member.qbk? Apart from being unnecessary, this will also mean that a bunch of files will need to be renamed if a new file is inserted or the files are reordered for some reason.  
  
I think he's following the convention from Beast, StaticString, Json etc.   I agree; I'm not a fan of that convention.

---

## Comment 14

> Username: alandefreitas  
> Created_at: 2021-10-12 01:11:20 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-940572559  

> What's the purpose of naming the qbk files 02_01_base_from_member.qbk instead of just base_from_member.qbk?   
  
@pdimov,   
  
@glenfe is correct. I was following that convention.  
  
I think it's OK to rename `02_01_base_from_member.qbk` to `base_from_member.qbk`. I think there's nothing in that convention that requires that to use docca.

---

## Comment 15

> Username: vinniefalco  
> Created_at: 2021-10-12 02:10:53 UTC  
> Updated_at: 2021-10-12 02:11:46 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-940593906  

> What's the purpose of naming the qbk files  
  
I do this to control the sort order for browsing the filenames. Yes if you insert or reorder, they have to be renamed. This doesn't happen often. This is what I see:  
  
![image](https://user-images.githubusercontent.com/1503976/136879413-731a6893-5a62-4796-8740-691150a7bafa.png)  
  
There is no need to follow this convention if that is not your preference.

---

## Comment 16

> Username: pdimov  
> Created_at: 2021-10-12 15:01:28 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-941095612  

I don't think that documentation updates in other libraries need to conform to your style or workflow. They will be maintained by other people, who have their own preferences.

---

## Comment 17

> Username: pdimov  
> Created_at: 2021-10-12 15:02:57 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-941096970  

I don't think that documentation updates in other libraries need to conform to your style or workflow. They will be maintained by other people, who have their own preferences.

---

## Comment 18

> Username: alandefreitas  
> Created_at: 2021-10-14 14:52:17 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-943435506  

> @Lastique  I'd prefer if we had the traditional layout, i.e. the docs and the Jamfile are in doc, and named accordingly. See Boost.Core for an example (which actually partly derived from Utility).  
  
>  @pdimov What's the purpose of naming the qbk files 02_01_base_from_member.qbk instead of just base_from_member.qbk?  
  
Both done in 39000e50d6a67f85dcb1aab759a4b3bd21b0ac0b  
  
Here's a more recent preview: https://alandefreitas.github.io/boost-utility-docs/  
New doc folder layout: https://github.com/alandefreitas/utility/tree/develop/doc  
Old docs for comparison: https://www.boost.org/doc/libs/1_77_0/libs/utility/

---

## Comment 19

> Username: pdimov  
> Created_at: 2021-10-14 15:18:25 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-943460123  

The new docs look spiffy.  
  
All this moving and renaming has the potential to lose history. You should remove the separate commits that first move the qbk files somewhere else and rename them, then move them back to their original names. I've checked `base_from_member.qbk` at random and `git` seems to have figured it out, but the commit history still shows unnecessary moves and renames.  
  
Similarly, you should leave `Jamfile.v2` under its original name, to retain its history. (Same applies to any other unnecessary moves and renames, if there are such.)  
  
I'm not fond of the new references. The combined reference doesn't make sense for Utility, because it's not a cohesive library. It's a collection of disparate components that had fit nowhere else, a relic from the old Boost days. These disparate components are completely independent and each should have its own reference.  
  
I also don't particularly like the component references. Taking for example `string_view`, at https://alandefreitas.github.io/boost-utility-docs/utility/ref/boost__string_view.html, it shows `const_iterator` as a hyperlink, and I need to click it, go to a [whole another page](https://alandefreitas.github.io/boost-utility-docs/utility/ref/boost__basic_string_view/const_iterator.html), which contains  
```  
using const_iterator = const_pointer;  
```  
I'd rather read this inline; it's not worth the cost of opening a new page.  
  
The common pattern here is that everything has been done the way Vinnie likes, which he might like, but other people do not. If the maintainers of Utility are fine with this (and don't mind the dependence on docca)... let them have it. I consider it... not the improvement we could have had.

---

## Comment 20

> Username: Lastique  
> Created_at: 2021-10-14 15:30:38 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-943471141  

[`basic_string_view`](https://alandefreitas.github.io/boost-utility-docs/utility/ref/boost__basic_string_view.html) description seems broken. I didn't look at every page to see if there are other broken docs.  
  
Also, if I click on the arrow up on the [`string_view`](https://alandefreitas.github.io/boost-utility-docs/utility/ref/boost__string_view.html) reference or many other reference pages, I get [here](https://alandefreitas.github.io/boost-utility-docs/utility/ref.html). And up from there, I get [here](https://alandefreitas.github.io/boost-utility-docs/utility_hidden.html). What are these pages? The up arrow should lead to the parent section or root of the library docs.  
  
And yes, I agree with Peter. I don't like the tabular view of the reference docs. I would rather have a Doxygen-style definition of the class with member docs below. [Here](https://www.boost.org/doc/libs/1_77_0/libs/log/doc/html/boost/log/core.html) is an example.

---

## Comment 21

> Username: Lastique  
> Created_at: 2021-10-14 15:35:22 UTC  
> Updated_at: 2021-10-14 15:36:12 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-943475119  

Also, reference docs are duplicated for some components on [this](https://alandefreitas.github.io/boost-utility-docs/utility/quickref.html) page. See `output_iterator_helper`, `typed_in_place_factory_base`, `value_initialized` and others. `string_view` is present in both Classes and Aliases.

---

## Comment 22

> Username: vinniefalco  
> Created_at: 2021-10-14 16:27:34 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-943519926  

> The common pattern here is that everything has been done the way Vinnie likes, which he might like  
  
I have no opinion on this and I made it clear to Alan that it was his choice - and it still is, if he wants to change it to asciidoc. Don't blame me!!!

---

## Comment 23

> Username: vinniefalco  
> Created_at: 2021-10-14 16:30:39 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-943522477  

The docca-style reference might not be appropriate for Boost.Utility, for the reasons you mentioned.

---

## Comment 24

> Username: pdimov  
> Created_at: 2021-10-14 16:37:13 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-943527789  

Asciidoc is not under consideration because (a) Andrey is not enthusiastic about it and (b) it doesn't integrate with Doxygen XML, but a more traditional reference (that doesn't use docca) should do fine.  
  
How are things going with that Doxygen killer?

---

## Comment 25

> Username: vinniefalco  
> Created_at: 2021-10-14 16:52:41 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-943539666  

> How are things going with that Doxygen killer?  
  
We are going to explore uh..... the thing. The copper thing. Doxypress, that's it. And we are exploring rendering directly to Docbook to have more formatting choices.

---

## Comment 26

> Username: pdimov  
> Created_at: 2021-10-14 16:55:06 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-943541920  

I would be more interested in rendering directly to asciidoc. Or, failing that, to something that can be easily converted to asciidoc.

---

## Comment 27

> Username: vinniefalco  
> Created_at: 2021-10-14 16:55:42 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-943542412  

> I would be more interested in rendering directly to asciidoc  
  
We will get right on that

---

## Comment 28

> Username: alandefreitas  
> Created_at: 2021-10-14 18:57:52 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-943635478  

> @pdimov All this moving and renaming has the potential to lose history. You should remove the separate commits that first move the qbk files somewhere else and rename them, then move them back to their original names. I've checked base_from_member.qbk at random and git seems to have figured it out, but the commit history still shows unnecessary moves and renames.  
>  
> Similarly, you should leave Jamfile.v2 under its original name, to retain its history. (Same applies to any other unnecessary moves and renames, if there are such.)  
  
I'll have a look at that. About half the .qbk files didn't exist before though. In the end, we can also squash these commits if that's better.  
  
> I'm not fond of the new references. The combined reference doesn't make sense for Utility, because it's not a cohesive library. It's a collection of disparate components that had fit nowhere else, a relic from the old Boost days. These disparate components are completely independent and each should have its own reference.  
  
I've categorized the contents now. That should look better: https://alandefreitas.github.io/boost-utility-docs/utility/quickref.html  
  
> I also don't particularly like the component references. Taking for example string_view, at https://alandefreitas.github.io/boost-utility-docs/utility/ref/boost__string_view.html, it shows const_iterator as a hyperlink, and I need to click it, go to a whole another page, which contains using const_iterator = const_pointer;  
I'd rather read this inline; it's not worth the cost of opening a new page.  
  
If the Javadocs are available, this information is also summarized [on the main string_view page](https://alandefreitas.github.io/boost-utility-docs/utility/ref/boost__string_view.html) under "description". The other page is there just to keep things uniform. In any case, if that's important, I can open an issue with a feature request on docca to see if something can be done about it.  
  
> The common pattern here is that everything has been done the way Vinnie likes, which he might like, but other people do not. If the maintainers of Utility are fine with this (and don't mind the dependence on docca)... let them have it. I consider it... not the improvement we could have had.  
  
> Asciidoc is not under consideration because (a) Andrey is not enthusiastic about it and (b) it doesn't integrate with Doxygen XML, but a more traditional reference (that doesn't use docca) should do fine.  
  
We can do things differently. But I did consider other options and criteria besides quickbook+dooca. I considered asciidoc and even markdown+doxybook2. The reason I chose quickbook, even though I don't even like it very much personally, is because it seemed less intrusive for Boost.Utility:  
  
- Boost.Utility was already using quickbook,   
- it was easier to build it with the existing boost toolset,   
- the results look like other boost documentation pages,   
- it could use docca for future javadocs, and  
- the quickbook macros are useful  
  
For instance, generating boost documentation from asciidoc or markdown with the usual boost documentation style still requires huge workarounds, like the ones in [boost.outcome](https://github.com/ned14/outcome/tree/develop/boostify). That's why I chose quickbook even though I wouldn't really use any of these tools if that wasn't a boost library.  
  
> but a more traditional reference (that doesn't use docca) should do fine  
  
What would that be? Not using doxygen?  
  
> @Lastique basic_string_view description seems broken. I didn't look at every page to see if there are other broken docs.  
  
That's correct. Everything looks fine if I change it from   
  
```cpp  
template<typename charT, typename traits>  
class basic_string_view;  
```  
  
to   
  
```cpp  
template<class charT, class traits>  
class basic_string_view;  
```  
  
![image](https://user-images.githubusercontent.com/5369819/137378611-2b001698-ff42-4f44-9026-61ddb62ba3ae.png)  
  
I'll open an issue on boostorg/docca.  
  
> Also, if I click on the arrow up on the string_view reference or many other reference pages, I get here. And up from there, I get here. What are these pages? The up arrow should lead to the parent section or root of the library docs.  
  
That's also how things are in the docca template. I initially just assumed there should be a good reason for that.   
  
So I removed the reference block when I realized the reference is linking to content in `utility/ref`, which are not effectively children of the Reference section. This actually wouldn't make sense because other sections, like Index, which could also be parents, also link to this content. So this is what happens if [these blocks](https://github.com/alandefreitas/utility/blob/62c718b64f04a7ea259ab38b73e9767070d788f5/doc/main.qbk#L233) are removed:  
  
![image](https://user-images.githubusercontent.com/5369819/137377699-8b6279b3-f659-4d0a-a01f-978e026efbdf.png)  
  
So (I imagine) that's why this black page is there. One more issue / question for docca then. By the way, I'm, as far as docca is concerned, I'm just a user. I don't really know the internals.  
  
> And yes, I agree with Peter. I don't like the tabular view of the reference docs. I would rather have a Doxygen-style definition of the class with member docs below. Here is an example.  
  
How are these generated? Or are they manually maintained?  
  
> Also, reference docs are duplicated for some components on this page. See output_iterator_helper, typed_in_place_factory_base, value_initialized and others. string_view is present in both Classes and Aliases.  
  
[Fixed](https://alandefreitas.github.io/boost-utility-docs/utility/quickref.html). 😀

---

## Comment 29

> Username: Lastique  
> Created_at: 2021-10-14 19:30:42 UTC  
> Updated_at: 2021-10-14 19:31:07 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-943661816  

>> Also, if I click on the arrow up on the string_view reference or many other reference pages, I get here. And up from there, I get here. What are these pages? The up arrow should lead to the parent section or root of the library docs.  
>  
> That's also how things are in the docca template. I initially just assumed there should be a good reason for that.  
  
Adding a hidden page to hide reference entries from TOC is one ugliest hack. If that's what it takes to generate reasonable reference, I would rather drop docca entirely in favor of Doxygen. Though I never used docca, and I don't know if there isn't a better way.  
  
Normally, you control the depth of sections you want to see in TOC through the `chunk.section.depth` property, see the example [here](https://github.com/boostorg/log/blob/4c54e4ca4c31f18ce2e451e8d0abf88054242d57/doc/Jamfile.v2#L271). Provided that docca generates BoostBook output, that property should work for it as well, making the hidden page hack unnecessary.  
  
> How are these generated?  
  
By running Doxygen on public headers. You can see the config in the Jamfile I linked above.

---

## Comment 30

> Username: sdarwin  
> Created_at: 2021-10-14 19:31:42 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-943662537  

btw, @Lastique or @pdimov , don't merge this until it's confirmed the docs are building correctly.  Right now, what we are seeing is that Alan can compile docs on his mac osx machine, but there is 1 error in a Linux environment.

---

## Review 31 [Commented]

> Username: Lastique  
> Created_at: 2021-10-14 19:47:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/utility/pull/79#pullrequestreview-780181951  

📁 throw_exception.html

```diff
  13 |- at <a href="http://www.boost.org/LICENSE_1_0.txt">www.boost.org/LICENSE_1_0.txt</a>)</p>
   9 |+ <a href="../throw_exception/doc/html/throw_exception.html#using_boost_throw_exception">../throw_exception/doc/html/throw_exception.html#using_boost_throw_exception</a>.&nbsp;<hr>
  10 |+ <p>� Copyright Beman Dawes, 2001</p>
```

> Username: Lastique  
> Created_at: 2021-10-14 19:47:33 UTC  
> Url: https://github.com/boostorg/utility/pull/79#discussion_r729290817  

Copyright character corrupted here.


---

## Review 32 [Commented]

> Username: Lastique  
> Created_at: 2021-10-14 19:50:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/utility/pull/79#pullrequestreview-780184371  

📁 doc/Jamfile.v2

```diff
   2 |+ # Copyright (c) 2019 Vinnie Falco (vinnie.falco@gmail.com)
   3 |+ # Copyright (c) 2021 Dmitry Arkhipov (grisumbras@gmail.com)
   4 |+ # Copyright (c) 2021 Alan Freitas (alandefreitas @ gmail.com)
```

> Username: Lastique  
> Created_at: 2021-10-14 19:50:16 UTC  
> Updated_at: 2021-10-14 20:18:27 UTC  
> Url: https://github.com/boostorg/utility/pull/79#discussion_r729292579  

Are spaces around @ intended? In other files too.


---

## Review 33 [Commented]

> Username: Lastique  
> Created_at: 2021-10-14 19:56:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/utility/pull/79#pullrequestreview-780189499  

📁 doc/compressed_pair.qbk

```diff
  83 |- type.
  71 |+ [@https://www.boost.org/doc/libs/1_77_0/libs/type_traits/doc/html/boost_typetraits/reference/is_union.html `boost::is_union`], or
  72 |+ if [@https://www.boost.org/doc/libs/1_77_0/libs/type_traits/doc/html/boost_typetraits/reference/is_union.html `boost::is_union`] is
```

> Username: Lastique  
> Created_at: 2021-10-14 19:56:09 UTC  
> Updated_at: 2021-10-14 19:56:10 UTC  
> Url: https://github.com/boostorg/utility/pull/79#discussion_r729296768  

You should avoid using version-specific links in the docs, as they will outdate every release. Use "release" instead of the version number to make them refer to the latest release. E.g. "https://www.boost.org/doc/libs/release/libs/type_traits/doc/html/boost_typetraits/reference/is_union.html".

> Username: Lastique  
> Created_at: 2021-10-14 19:59:54 UTC  
> Updated_at: 2021-10-14 19:59:55 UTC  
> Url: https://github.com/boostorg/utility/pull/79#discussion_r729299150  

Though, in this case I would probably make the link point at Boost.TypeTraits docs instead of specifically at `is_union` page, as this link is too fragile and may break if Boost.TypeTraits docs are modified. You can adjust the text to make it look like "`boost::is_union` from [@... Boost.TypeTraits], or if `boost::is_union` is specialized..." to avoid duplication.


---

## Review 34 [Commented]

> Username: Lastique  
> Created_at: 2021-10-14 20:01:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/utility/pull/79#pullrequestreview-780194478  

📁 doc/main.qbk

```diff
  32 |+ 
  33 |+ [template include_file[path][^<'''<ulink url="https://github.com/cppalliance/utility/blob/master/include/'''[path]'''">'''[path]'''</ulink>'''>]]
  34 |+ [template issue[n] '''<ulink url="https://github.com/cppalliance/utility/issues/'''[n]'''">#'''[n]'''</ulink>''']
```

> Username: Lastique  
> Created_at: 2021-10-14 20:01:50 UTC  
> Url: https://github.com/boostorg/utility/pull/79#discussion_r729300455  

These links should point to boostorg, not cppalliance.


---

## Review 35 [Commented]

> Username: Lastique  
> Created_at: 2021-10-14 20:05:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/utility/pull/79#pullrequestreview-780198195  

📁 doc/main.qbk

```diff
 160 |+ [def __wstring_ref__ [link utility.ref.boost__wstring_ref `wstring_ref`]]
 161 |+ [def __wstring_view__ [link utility.ref.boost__wstring_view `wstring_view`]]
 162 |+ 
```

> Username: Lastique  
> Created_at: 2021-10-14 20:05:44 UTC  
> Updated_at: 2021-10-14 20:05:45 UTC  
> Url: https://github.com/boostorg/utility/pull/79#discussion_r729302961  

These could be auto-generated with a stylesheet like [this](https://github.com/boostorg/log/blob/4c54e4ca4c31f18ce2e451e8d0abf88054242d57/doc/gen_references.xsl) from Doxygen output. If not, they could probably be moved to the files corresponding to the respective components.

> Username: alandefreitas  
> Created_at: 2021-10-30 12:26:55 UTC  
> Updated_at: 2021-10-30 12:26:56 UTC  
> Url: https://github.com/boostorg/utility/pull/79#discussion_r739647271  

There are lots of quickbook files linking to the same macros now, such as overview.qbk, quickref.xml, index.xml...


---

## Review 36 [Commented]

> Username: Lastique  
> Created_at: 2021-10-14 20:09:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/utility/pull/79#pullrequestreview-780201515  

📁 doc/operators.qbk

```diff
1825 |+ # [#note:1] Unlike other iterator helpers templates, __output_iterator_helper__ takes only one template parameter -
1826 |+ the type of its target class. Although to some it might seem like an unnecessary restriction, the standard requires
1827 |+ `difference_type` and `value_type` of any output iterator to be `void` (24.3.1 [lib.iterator.traits]), and
```

> Username: Lastique  
> Created_at: 2021-10-14 20:09:34 UTC  
> Updated_at: 2021-10-14 20:09:35 UTC  
> Url: https://github.com/boostorg/utility/pull/79#discussion_r729305381  

Don't you need to escape `[` and `]` in QuickBook? In other places as well.

> Username: alandefreitas  
> Created_at: 2021-10-30 12:25:13 UTC  
> Url: https://github.com/boostorg/utility/pull/79#discussion_r739647174  

I think it looks correct.   
  
- [#note:1], [#note:2], ... create archors to [this](https://alandefreitas.github.io/boost-utility-docs/utility/utilities/operators.html#note:1) and   
- `[lib.iterator.traits]` renders as "[lib.iterator.traits]", which is what the original text looked like. This wasn't a link. I mean, unless there is some other problem with not escaping `[` `]` I don't know about.


---

## Review 37 [Commented]

> Username: Lastique  
> Created_at: 2021-10-14 20:10:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/utility/pull/79#pullrequestreview-780201915  

📁 doc/operators.qbk

```diff
1834 |+ to reduce the task of writing an output iterator to writing just two member functions - an appropriate
1835 |+ constructor and a copy-assignment operator. For example, here is a possible implementation of
1836 |+ [@../../..iterator/doc/function_output_iterator.html `boost::function_output_iterator`] adaptor:
```

> Username: Lastique  
> Created_at: 2021-10-14 20:10:00 UTC  
> Updated_at: 2021-10-14 20:10:01 UTC  
> Url: https://github.com/boostorg/utility/pull/79#discussion_r729305695  

Slash missing in the link?


---

## Review 38 [Commented]

> Username: Lastique  
> Created_at: 2021-10-14 20:13:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/utility/pull/79#pullrequestreview-780205044  

📁 doc/overview.qbk

```diff
  23 |+ [table:id Components
  24 |+     [[Boost.Utility] [Moved to Boost] [C++ Standard variant]]
  25 |+     [[[@../core/doc/html/core/addressof.html `addressof`]] [[@../core/index.html Boost.Core]] [C++11 __std_addressof__]]
```

> Username: Lastique  
> Created_at: 2021-10-14 20:13:43 UTC  
> Updated_at: 2021-10-14 20:13:44 UTC  
> Url: https://github.com/boostorg/utility/pull/79#discussion_r729308114  

I suspect, this and other links in this file will be broken. "libs/utility/doc/../core" is not a valid location. Or will it be "libs/utility/doc/html/../core" in the generated html? You should probably use version-neutral links to the website.

> Username: vinniefalco  
> Created_at: 2021-10-14 20:16:48 UTC  
> Url: https://github.com/boostorg/utility/pull/79#discussion_r729310072  

You can link to other boost libraries relative to the current commit or version using `@boost`. Here I link to an Asio page. This will always produce the correct URL as the Boost version or commit evolves:  
```  
[@boost:/doc/html/boost_asio/reference/strand.html `net::strand`]  
```


---

## Comment 39

> Username: alandefreitas  
> Created_at: 2021-10-14 21:39:52 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-943756257  

@Lastique Thank you for the review. I'm on it.

---

## Comment 40

> Username: vinniefalco  
> Created_at: 2021-10-14 23:26:36 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-943836883  

> Adding a hidden page to hide reference entries from TOC is one ugliest hack.  
  
Yeah it is. I don't know how to do it any other way. My first exposure to Quickbook, Boost.Book, and DocBook was with my first Boost library.  
  
> If that's what it takes to generate reasonable reference, I would rather drop  
> docca entirely in favor of Doxygen. Though I never used docca, and I don't  
> know if there isn't a better way.  
  
This actually has nothing to do with docca. If you are willing to just turn TOC off and write your table of contents manually then you don't need the hack. This is the approach used by Asio (which doesn't use docca), https://github.com/boostorg/asio/blob/a6f695ae557f4d9c4009fcafaaf859ebefd8e04b/doc/asio.qbk#L53  
  
Perhaps there is a way to get the doc toolchain to emit the table of contents but leave it out on the generated references pages, but I am not familiar enough with it to know.  
  
> Normally, you control the depth of sections you want to see in TOC through the   
>`chunk.section.depth` property, see the example.... Provided that docca generates  
> BoostBook output, that property should work for it as well, making the hidden  
> page hack unnecessary.  
  
Docca generates Quickbook not Boost.Book. But I am very happy to see any patch that lets me get rid of the hack that keeps the auto-generated TOC without spilling over onto the reference pages!!! Teach me :)  
  
Thanks

---

## Comment 41

> Username: Lastique  
> Created_at: 2021-10-15 09:34:17 UTC  
> Updated_at: 2021-10-15 09:40:13 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-944148113  

I don't know if there is a way to limit the TOC depth selectively for Reference section, I don't think there is. But limiting the depth to 2 levels for the whole TOC works well most of the time. Regular documentation rarely nest sections much deeper, and for Reference you either get subsections of references (e.g. like [here](https://www.boost.org/doc/libs/1_77_0/libs/log/doc/html/index.html)) or a list of headers (similar to what you get [here](https://www.boost.org/doc/libs/1_77_0/doc/html/intrusive.html)). [Boost.Interprocess](https://www.boost.org/doc/libs/1_77_0/doc/html/interprocess.html), actually, neatly sidesteps the problem by moving the actual Reference one level deeper in the hierarchy, along with an Index section.  
  
In Boost.Utility, it would make sense to split the Reference section into separate reference sections, one for each component. This would move the generated reference under each component's parent section, and work in favor of omitting the list of headers from the global TOC. You just need to limit `chunk.section.depth` to an appropriate value. The downside of this is you have to list the headers corresponding to each component in the Jamfile, but I think we can live with that.  
  
If docca generates QuickBook then the property should work for it too, as well as the workarounds I mentioned. But the screenshot @alandefreitas showed indicates that the generated sections have broken names sometimes, which will get exposed in the nested TOCs, and there are way too many of them. I suspect, you generate a separate section for each symbol because you want its docs on a separate page. Maybe you can work around the global TOC issue by moving these sections to a [deeper heading level](https://www.boost.org/doc/libs/1_77_0/doc/html/quickbook/syntax/block.html#quickbook.syntax.block.headings) instead of hiding them under a hidden page, and limiting the TOC depth above that level. Actually, I suspect headings of any level don't get represented in the global TOC. But in Boost.Utility, I think, pure Doxygen output is more preferable.

---

## Comment 42

> Username: glenfe  
> Created_at: 2021-10-15 12:33:11 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-944260861  

I would prefer we don't introduce a docca requirement into Utility's documentation. Another tool in the process alone would give me pause, but that docca requires Java et. al. makes it less palatable.   
  
I'd prefer keeping to just Doxygen plus Quickbook (a la Core) - and I wouldn't be opposed to Asciidoc with hand written reference documentation  in the style of Smart_Ptr, Endian, IO etc.

---

## Comment 43

> Username: Lastique  
> Created_at: 2021-10-15 13:00:07 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-944277594  

Hand written reference can easily get out of sync with the actual code, and is an extra source of work to maintain. I firmly believe in auto-generated reference docs.

---

## Comment 44

> Username: alandefreitas  
> Created_at: 2021-10-30 12:31:28 UTC  
> Updated_at: 2021-10-30 16:36:46 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-955202104  

# A small update  
  
[Preview](https://alandefreitas.github.io/boost-utility-docs/index.html)  
  
## Issues Fixed  
  
Most important issues fixed:  
  
- Split, pack, and beautify reference ([Commit](https://github.com/alandefreitas/utility/commit/62c718b64f04a7ea259ab38b73e9767070d788f5), [Preview](https://alandefreitas.github.io/boost-utility-docs/utility/index_and_ref/ref.html))  
- [Fix basic_string_view description](https://github.com/boostorg/utility/pull/79#issuecomment-943635478) ([Commit](https://github.com/boostorg/docca/commit/5e913afffd6282a4f776203a6dfc3351b00d27c3), [Preview](https://alandefreitas.github.io/boost-utility-docs/utility/index_and_ref/ref/boost__basic_string_view.html))  
- [Missing copyright and @ in email addresses](https://github.com/boostorg/utility/pull/79#discussion_r725643283) ([Commit](https://github.com/alandefreitas/utility/commit/7d01979dc2afce050b7f61cc93443bf7a5b22e50))  
- [Replace cppalliance links with boostorg](https://github.com/boostorg/utility/pull/79#discussion_r729300455) ([Commit](https://github.com/alandefreitas/utility/commit/7d01979dc2afce050b7f61cc93443bf7a5b22e50), [Preview](https://alandefreitas.github.io/boost-utility-docs/index.html))  
- [Use `@boost` in relative links](https://github.com/boostorg/utility/pull/79#discussion_r729310072) ([Commit](https://github.com/alandefreitas/utility/commit/7d01979dc2afce050b7f61cc93443bf7a5b22e50), [Preview](https://alandefreitas.github.io/boost-utility-docs/utility/utilities/base_from_member.html))  
- [Fix corrupted copyright char in comments](https://github.com/boostorg/utility/pull/79#discussion_r729290817) ([Commit](https://github.com/alandefreitas/utility/commit/3c52f3439d1403227a76bd1096f6af887a685311))  
- Fix all quickbook link warnings and reference glitches ([Commit](https://github.com/alandefreitas/utility/commit/006d1a766c70d88812d69fcd6bb3476400529ae6), [Commit](https://github.com/alandefreitas/utility/commit/2356bed26304a955913a6770a80c3f530d282a32), [Commit](https://github.com/alandefreitas/utility/commit/fc3ad5b14393d273e1e452374d00feeb60a2031a), [Preview](https://alandefreitas.github.io/boost-utility-docs/utility/index_and_ref/ref/boost__operators_lt_T_comma__T__gt_.html))  
- [Move reference to subsection](https://github.com/boostorg/utility/pull/79#issuecomment-944148113) / [hide pages from TOC](https://github.com/boostorg/utility/pull/79#issuecomment-944148113) / [Remove reference blank page](https://github.com/boostorg/utility/pull/79#issuecomment-943471141) / [Fix up arrow link](https://github.com/boostorg/utility/pull/79#issuecomment-943471141) ([Commit](https://github.com/alandefreitas/utility/commit/a36f2dc888c5f4ff44567d172bd2f4d727f2bbbd), [Commit](https://github.com/alandefreitas/utility/commit/094407e1b79fdec6fb8944917a10cfe819d46292), [Preview TOC](https://alandefreitas.github.io/boost-utility-docs/index.html), [Preview Ref](https://alandefreitas.github.io/boost-utility-docs/utility/index_and_ref/ref.html))  
  
## New issues  
  
Maybe open new issue for these:  
  
- Use [`empty_value`](https://www.boost.org/doc/libs/develop/libs/core/doc/html/core/empty_value.html) contributed by @glenfe [here](https://github.com/boostorg/utility/blob/c960bef6ef005413d96cc7366b66cbdf56d47110/include/boost/operators.hpp#L141)  
  
## Todo / Before merging  
  
- Squash commits [as I promised](https://github.com/boostorg/utility/pull/79#issuecomment-943635478) @pdimov  
- Let @sdarwin [ensure this is OK](https://github.com/boostorg/utility/pull/79#issuecomment-943662537)

---

## Comment 45

> Username: sdarwin  
> Created_at: 2021-10-30 14:48:16 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-955256709  

@alandefreitas , still getting "...failed updating 1 target...".     That aws test instance is running.  You could copy scripts/ci.yml from there into this pull request at .github/workflows/ci.yml, so Github actions will show if the build succeeds or fails on each commit.    Also, run tests directly on that server.

---

## Comment 46

> Username: Lastique  
> Created_at: 2021-10-30 15:44:25 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-955356777  

> You could copy scripts/ci.yml from there into this pull request at .github/workflows/ci.yml  
  
GHA is already enabled for Boost.Utility. Just rebase the PR on top of the current develop.

---

## Comment 47

> Username: vinniefalco  
> Created_at: 2021-10-30 15:58:26 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-955380606  

Just a thought. The quick reference column headers show identifiers, it might look better if they showed proper tiles. Currently:  
  
![image](https://user-images.githubusercontent.com/1503976/139540380-f8bced43-21a9-4f55-bcba-ac640f273201.png)  
  
You might try changing those titles to, e.g.:  
  
"Compare Pointees", "Compressed Pair", "In-place Factory"  
  
to see how it looks.

---

## Comment 48

> Username: alandefreitas  
> Created_at: 2021-10-30 16:34:58 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-955441297  

> You might try changing those titles to, e.g.:  
> "Compare Pointees", "Compressed Pair", "In-place Factory"  
> to see how it looks.  
  
That's what it looks like. We can also do it for the TOC. It's up to you guys.  
  
Before:  
  
<img width="968" alt="image" src="https://user-images.githubusercontent.com/5369819/139541240-efdab591-638d-44d4-a203-7a4a0af73ac6.png">  
  
After:  
  
<img width="968" alt="image" src="https://user-images.githubusercontent.com/5369819/139541299-abc052a9-6761-4d27-a45e-049f2f156bba.png">  
  
  
Before:  
  
<img width="314" alt="image" src="https://user-images.githubusercontent.com/5369819/139541351-2f61de64-287b-4f8d-97c6-31ecc779729b.png">  
  
  
After:  
  
<img width="317" alt="image" src="https://user-images.githubusercontent.com/5369819/139541391-f11cb4db-8ac7-4378-8fc1-3a9baf521e89.png">  
  
`BOOST_BINARY` also looks a little weird in the TOC. It's a little redundant since everything is also "Boost". "Binary Integer Literals", like in [cppreference](https://en.cppreference.com/w/cpp/language/integer_literal), might be a better section name:  
  
<img width="273" alt="image" src="https://user-images.githubusercontent.com/5369819/139541524-734a2f76-0358-4d3e-aa61-dccdfa0c2c3a.png">

---

## Comment 49

> Username: alandefreitas  
> Created_at: 2021-10-30 16:39:12 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-955448303  

> run tests directly on that server.  
  
> GHA is already enabled for Boost.Utility. Just rebase the PR on top of the current develop.  
  
OK. I'll do both.

---

## Comment 50

> Username: sdarwin  
> Created_at: 2021-10-30 16:40:41 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-955450783  

> GHA is already enabled for Boost.Utility. Just rebase the PR on top of the current develop.  
  
The version of the code in this pull request already has a copy of GHA.    
  
So, adding the file mentioned earlier will look like a "merge", instead of an entirely new file.   It includes a "docs" CI job.

---

## Comment 51

> Username: vinniefalco  
> Created_at: 2021-10-30 16:42:25 UTC  
> Updated_at: 2021-10-30 16:42:37 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-955453627  

no merge commits please

---

## Comment 52

> Username: vinniefalco  
> Created_at: 2021-10-30 16:43:15 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-955455005  

I like the new titles but if the maintainers of Utility feel otherwise, that's fine too.

---

## Comment 53

> Username: alandefreitas  
> Created_at: 2021-10-30 16:44:16 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-955456650  

> no merge commits please   
  
Yes. It's in the todo list before merging:  
  
> - Squash commits as I promised @pdimov  
> - Let @sdarwin ensure this is OK

---

## Comment 54

> Username: glenfe  
> Created_at: 2021-10-30 16:47:13 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-955461439  

Trying the PR locally and:  
  
> saxonhe.saxonhe ../../bin.v2/libs/utility/doc/_reference-dir/xml-pages.xml  
> /bin/sh: line 2: -jar: command not found

---

## Comment 55

> Username: vinniefalco  
> Created_at: 2021-10-30 16:56:39 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-955477241  

install the toolchain bro

---

## Comment 56

> Username: glenfe  
> Created_at: 2021-10-30 17:11:08 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-955500927  

I think I was under the impression that saxonhe wouldn't be involved for Utility. But if @Lastique  has no objections then I won't either.

---

## Comment 57

> Username: Lastique  
> Created_at: 2021-10-30 17:31:47 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-955534720  

> > GHA is already enabled for Boost.Utility. Just rebase the PR on top of the current develop.  
>   
> The version of the code in this pull request already has a copy of GHA.  
>   
> So, adding the file mentioned earlier will look like a "merge", instead of an entirely new file. It includes a "docs" CI job.  
  
I would prefer if the PR was a straight patch on top of the current develop, focused on docs changes. If there is something you'd like to add to CI, make it a separate PR or, if it is directly related to this PR, make it a change to the current `ci.yml` in Boost.Utility.

---

## Comment 58

> Username: Lastique  
> Created_at: 2021-10-30 17:34:53 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-955539789  

> I think I was under the impression that saxonhe wouldn't be involved for Utility. But if @Lastique has no objections then I won't either.  
  
I have no idea what this is and I would prefer not to have to know. Just make it a simple QuickBook+Doxygen please, with no fancy bells and whistles.

---

## Comment 59

> Username: alandefreitas  
> Created_at: 2021-10-30 17:39:45 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-955547626  

I kept docca because there seemed to be no consensus on the alternative. Everyone proposed something different.  
  
On the other hand, docca is officially in the boost toolset anyway and its reference looks quite good, although that's a matter of taste, of course.  
  
But that's no problem at all. I'll start another reference. I'll assume the consensus is a simple QuickBook+Doxygen then.

---

## Comment 60

> Username: sdarwin  
> Created_at: 2021-10-30 17:49:31 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-955563281  

> make it a change to the current ci.yml in Boost.Utility.  
  
It is directly related to this PR (whether this PR builds or not), and should appear merely as a change  in ci.yml, not an entirely different file.

---

## Comment 61

> Username: glenfe  
> Created_at: 2021-10-30 17:58:28 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-955570170  

@alandefreitas If it is simple Quickbook+Doxygen then I can help maintain it. And test out the PR locally, etc.

---

## Comment 62

> Username: alandefreitas  
> Created_at: 2021-11-11 21:19:46 UTC  
> Updated_at: 2021-11-11 21:20:35 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-966633163  

# More updates  
  
- Reference is just Quickbook+Doxygen now  
- Each Boost.Utility component includes its own reference  
- A quick reference is still included at the end with links to component pages  
- Old commits were squashed to improve history  
  
Preview: https://alandefreitas.github.io/boost-utility-docs/

---

## Comment 63

> Username: glenfe  
> Created_at: 2021-11-12 02:46:01 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-966776094  

@alandefreitas - the output looks good to me. There are some warnings though:  
  
```  
xslt-xsltproc ../../bin.v2/libs/utility/doc/main.docbook  
Warning: missing 'type' element inside 'inherit'  
  In boost::compressed_pair::  
Warning: missing 'type' element inside 'inherit'  
  In boost::compressed_pair<T, T>::  
xslt-xsltproc-dir doc/html/standalone_main_HTML.manifest  
../../bin.v2/libs/utility/doc/main.docbook:852: element section: validity error : ID utility.reference already defined  
      <section id="utility.reference"><title>Reference</title><section id="heade  
                                     ^  
../../bin.v2/libs/utility/doc/main.docbook:2088: element section: validity error : ID utility.reference already defined  
      <section id="utility.reference"><title>Reference</title>  
                                     ^  
../../bin.v2/libs/utility/doc/main.docbook:2215: element section: validity error : ID utility.reference already defined  
      <section id="utility.reference"><title>Reference</title>  
                                     ^  
../../bin.v2/libs/utility/doc/main.docbook:2614: element section: validity error : ID utility.reference already defined  
      <section id="utility.reference"><title>Reference</title><section id="heade  
                                     ^  
../../bin.v2/libs/utility/doc/main.docbook:6410: element section: validity error : ID utility.reference already defined  
      <section id="utility.reference"><title>Reference</title><section id="heade  
                                     ^  
../../bin.v2/libs/utility/doc/main.docbook:6725: element section: validity error : ID utility.reference already defined  
      <section id="utility.reference"><title>Reference</title><section id="heade  
                                     ^  
../../bin.v2/libs/utility/doc/main.docbook:7667: element section: validity error : ID utility.reference already defined  
      <section id="utility.reference"><title>Reference</title><section id="heade                                     ^  
```

---

## Comment 64

> Username: vinniefalco  
> Created_at: 2021-11-12 07:09:30 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-966875162  

Nice work!

---

## Comment 65

> Username: alandefreitas  
> Created_at: 2021-11-12 15:32:33 UTC  
> Updated_at: 2021-11-15 18:34:03 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-967206758  

> There are some warnings though  
  
@glenfe Both warnings are related to the doxygen xml output. I looked all around for solutions but couldn't remove the warnings. Maybe someone more experienced in doxygen/quickbook can fix that. I ensured they don't affect the output though.  
  
## Details  
  
### Reference id warning  
  
*The cause*: The second error (`<section id="utility.reference"><title>Reference</title><section id="heade`) is simpler. As suggested, I reused the structure of Boost.Core for the documentation of the Boost.Utility components. But Boost.Core only has a Doxygen documentation for one of its components (`ref`) while I included the Doxygen reference for all Boost.Utility components. This causes all components references to have the same id for their root element (`utility.reference`).   
  
*Some solutions I tried*: The warning about duplicated ids is correct but it doesn't affect the output because this root id is never used in any links. This is not a problem in Boost.Core only because it includes the reference for only one of its components. So I know why this one happens but I couldn't find any solutions to remove this warning. This id does not come from the `xml` file doxygen generates. Quickbook has a syntax to define the id of documents we `include` (`[include:someid someother.qbk]`) but the same didn't work for `xinclude`.   
  
### compressed_pair warning (fixed)  
  
*The cause*: The first error (`Warning: missing 'type' element inside 'inherit' In boost::compressed_pair::`) is supposed to be in the intermediary `doc/main.docbook`. As usual, it only happens when I include the doxygen reference (`[xinclude compressed_pair_reference.xml]`).  
  
*Some solutions I tried*: I couldn't find any problem with this element in the intermediary `main.docbook` or in `doc`. I also tried looking for similar doxygen patterns in other components, but none of the other similar components generate this warning. I've looked all around about this kind of warning but couldn't find anything, as usual with these tools. I check the [reference output](https://alandefreitas.github.io/boost-utility-docs/utility/utilities/compressed_pair.html#utility.reference) for compressed_pair and it still looks ok though.

---

## Comment 66

> Username: alandefreitas  
> Created_at: 2021-11-15 18:47:21 UTC  
> Updated_at: 2021-11-15 18:55:26 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-969210204  

@glenfe   
  
I managed to fix the compressed_pair warning. I'm trying to implement your idea to fix the warnings for the duplicated ids now.   
  
### Where the problem seems to be:  
  
- There's no `utility.reference` in the doxygen xml files or in the `main.xml` file quickbook generates, which just converts the quickbook `xinclude` to the boostbook `xi:include`.   
- The `utility.reference` id only appears in `main.docbook`, where the `xi:include` has already been replaced with the doxygen content.   
   
So if I understand it correctly, this is either boostbook's or docbook's fault, when converting the `xi:include`s.  
  
#### Solution 1) doc info block in each component  
  
So the first thing I tried was to include a doc info block for each component requesting quickbook 1.6 because of [this section](https://www.boost.org/doc/libs/1_74_0/doc/html/quickbook/syntax/block.html#quickbook.syntax.block.include) in the quickbook documentation:  
  
> In quickbook 1.6 and later, if the included file has a docinfo block then it will create a nested document. This will be processed as a standalone document, although any macros or templates from the enclosing file will still be defined  
  
**The problem** is it still generates the `id utility.reference` for these references even though they are standalone documents now. It inherits it from the utility prefix from the parent file.  
  
#### Solution 2) compile each component separately  
  
The second idea was to compile each component is if it were the complete documentation (like `xml base_from_member : base_from_member.qbk`, ...) and then including their `xml` files in `main.qbk` with `xinclude` instead of `include` (replacing `[include base_from_member.qbk]` with `[xinclude base_from_member.xml]`, ...).  
  
**The problem** is this led to lots of new warnings and problems. 1) Some warnings were because the new standalone docs don't have access to macros, templates, and anchors from the enclosing file, like in the first solution. 2) I imagine this would also make the quick reference impossible. 3) But most of the new warnings are still greek to me. I'd have to go through all of them. 4) The `xinclude`s are also not working for the new `.xml` files. It doesn't give me any warnings but the component just disappears from the main table of contents.  
  
#### Overall conclusion  
  
Part of that is probably because I'm doing something wrong. I'm not proficient with anything in `<boost_root>/tools` at all. I read their documentation but it's like I learned almost nothing from it. I keep replicating patterns from other libs to try to focus on the documents. So there's that.  
  
On the other hand, something doesn't smell good about this second solution. It looks like it might be too much of a workaround for something that looks too simple. At least in the sense that we would need to completely change the document structure just to fix a warning caused by a single identifiable `id` that has no effect on the output at all.   
  
In the future, it seems like even some small script to remove the substring ` id="utility.reference"` from `main.docbook` or transformation to remove the `utility.reference` id from the section tags would be less of a workaround (even though these are huge workarounds). In any case, there are no links to this `utility.reference` id and none of that affects the output.

---

## Comment 67

> Username: glenfe  
> Created_at: 2021-11-16 00:49:49 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-969549034  

I'm not sure this statement is correct:  
  
> At least in the sense that we would need to completely change the document structure just to fix a warning caused by a single identifiable id that has no effect on the output at all.  
  
It might be true based on the TOC depth when generating the HTML, because each `<a name="utility.reference">`   
 is in its own HTML file. But if the depth was configured for everything in a single page, then wouldn't that mean that there would be multiple `<a name="utility.reference">` in the same page?   
  
I agree this is a limitation of our current tooling. It always generates "reference" and then based on the article/library id becomes "id.reference". It would be better to either:  
  
* Be able to specify that it generates "subthing.reference" or  
* When `xinclude`  be able to say something like `[xinclude:subthing string_view_reference.xml]`   
  
Maybe someone else has some better solution. Posting to the boost developer list might asking for suggestions might be the way to go.

---

## Comment 68

> Username: pdimov  
> Created_at: 2021-11-16 01:13:41 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-969588331  

`[xinclude thing.xml]` just translates to `<xi:include href="thing.xml">`, so it can't override the `id` of the `<library-reference>` tag inside `thing.xml`.

---

## Comment 69

> Username: alandefreitas  
> Created_at: 2021-11-16 01:13:58 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-969588881  

> I'm not sure this statement is correct  
  
It's probably not. 😀 I don't have a lot of experience with the workflow.  
  
> But if the depth was configured for everything in a single page, then wouldn't that mean that there would be multiple <a name="utility.reference"> in the same page?  
  
If there are two anchors with the same name on an html page, does this cause any problems we don't already have?   
  
There are no links to "utility.reference" in the documentation and we can always create new anchors in Quickbook so there's no reason to ever create links to "utility.reference". But even if there were links to "utility.reference" *and* these anchors were generated in the same html page, I just imagine the link would go to the first anchor.  
  
> TOC depth when generating the HTML  
  
I haven't even thought of that problem yet to be honest. I was thinking more about that small list of problems I had so far:   
  
> > The problem is this led to lots of new warnings and problems. 1) Some warnings were because the new standalone docs don't have access to macros, templates, and anchors from the enclosing file, like in the first solution. 2) I imagine this would also make the quick reference impossible. 3) But most of the new warnings are still greek to me. I'd have to go through all of them. 4) The xincludes are also not working for the new .xml files. It doesn't give me any warnings but the component just disappears from the main table of contents.  
  
Basically, even if it works, the individual components can't link to each other, which imposes some limitations, like the quick reference becoming impossible or components referencing each other. That's assuming I manage to fix the problem where the individual components also disappeared from the main TOC and the new warning, which I have no idea how to fix yet. That's why it seems like it's not worth doing it just to remove that warning.  
  
> I agree this is a limitation of our current tooling. It always generates "reference" and then based on the article/library id becomes "id.reference".  
  
Yes. It's almost unbelievable that there's no alternative to this. Especially because the problem seems to be in the boostbook or docbook steps (I might be wrong again). It's hard to imagine no one ever included more than one `xml` in their documentation. I keep thinking someone probably knows a trivial solution to this. I'm just not seeing it.

---

## Comment 70

> Username: alandefreitas  
> Created_at: 2021-11-16 01:24:07 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-969608204  

> [xinclude thing.xml] just translates to <xi:include href="thing.xml">, so it can't override the id of the <library-reference> tag inside thing.xml.  
  
Exactly.   
  
There's no `utility.reference` in the `main.xml` quickbook generates.   
  
```xml  
<xi:include href="../../../../libs/utility/doc/base_from_member_reference.xml"/>  
```  
  
The `utility.reference` id only exists in the final `main.docbook`.   
  
```xml  
<section id="utility.reference"><title>Reference</title><section id="header.boost.utility.base_from_member_hpp"><title>Header &lt;<ulink url="../../../../boost/utility/base_from_member.hpp">boost/utility/base_from_member.hpp</ulink>&gt;</title><synopsis xmlns:xi="http://www.w3.org/2001/XInclude">  
```  
  
So the warnings seem to be at the boostbook or docbook step.  
  
I even looked at the docbook [`xi:include` documentation](http://www.sagehill.net/docbookxsl/ModularDoc.html) but couldn't find anything that helps.

---

## Comment 71

> Username: pdimov  
> Created_at: 2021-11-16 01:36:15 UTC  
> Updated_at: 2021-11-16 01:37:16 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-969629035  

`base_from_member_reference.xml` contains `<library-reference>`. This tag [has an optional `id` attribute](https://www.boost.org/doc/libs/1_77_0/doc/html/boostbook/dtd/library-reference.html). This attribute [seems to be settable](https://github.com/boostorg/boostbook/blob/aa3d1d676ce2d921dc820bbea9ed38a39a279402/xsl/doxygen/doxygen2boostbook.xsl#L41-L43) via the `boost.doxygen.refid` `xsl:param` of `doxygen2boostbook.xsl`. (It can also take its value from the `boost.doxygen.reftitle` `xsl:param`, which [seems to be settable](https://github.com/boostorg/build/blob/3dd9a8fc7f382342c672297de2d0ef72733a9509/src/tools/doxygen.jam#L428-L431) via the `<reftitle>` feature, but we probably don't need that.)

---

## Comment 72

> Username: alandefreitas  
> Created_at: 2021-11-16 02:00:16 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-969672707  

I tried to set it [like this](https://github.com/boostorg/stacktrace/blob/3f3f9020fbec1fe36800aca52054eb159db6af2d/doc/Jamfile.v2#L33) but it hasn't worked yet.  
  
For instance,   
  
```  
doxygen base_from_member_reference  
  :  
    [ glob ../include/boost/utility/base_from_member.hpp ]  
  :  
    <doxygen:param>ENABLE_PREPROCESSING=YES  
    # ...  
     <xsl:param>"boost.doxygen.reftitle=Reference"  
     <xsl:param>"boost.doxygen.refid=base_from_member.reference"  
  ;  
```  
  
still generates   
  
```xml  
<library-reference><header name="boost/utility/base_from_member.hpp">  
<!--  ...  -->  
</library-reference>  
```  
  
😔

---

## Comment 73

> Username: glenfe  
> Created_at: 2021-11-16 02:12:26 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-969683660  

It seems to work for Boost.Stacktrace which has in its Jamfile:  
  
```  
<xsl:param>"boost.doxygen.refid=stacktrace.reference"  
```  
  
This generates in the XML:  
  
```  
<library-reference id="stacktrace.reference">  
```

---

## Comment 74

> Username: pdimov  
> Created_at: 2021-11-16 02:12:41 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-969683907  

You need to put it on the `boostbook` rule, not on the `doxygen` rule. The `doxygen` rule takes the headers and generates doxygen XML; the `boostbook` rule runs the doxygen XML through the XSLT and generates boostbook XML.

---

## Comment 75

> Username: pdimov  
> Created_at: 2021-11-16 02:13:23 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-969684620  

> It seems to work for Boost.Stacktrace  
  
Someone has figured this stuff out. :-)

---

## Comment 76

> Username: pdimov  
> Created_at: 2021-11-16 02:19:04 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-969690709  

> You need to put it on the boostbook rule, not on the doxygen rule.  
  
Or not. Looks like the `doxygen` is the right place to put it. No idea why it doesn't work.

---

## Comment 77

> Username: pdimov  
> Created_at: 2021-11-16 02:31:16 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-969708389  

Yeah, `doxygen` is correct, and I got this to work (using the Core docs as the guinea pig) from the fourth try or so. Basically, `refid` is ignored if there's no `reftitle` (which is stupid), and if you just edit the Jamfile, the .xml is not regenerated, so you need to delete the .boostbook file from `bin.v2/libs/whatever/doc` in order to force it to rebuild.

---

## Comment 78

> Username: alandefreitas  
> Created_at: 2021-11-16 05:00:33 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-969861068  

> if you just edit the Jamfile, the .xml is not regenerated, so you need to delete the .boostbook file from bin.v2/libs/whatever/doc in order to force it to rebuild.  
  
That was it. 😃 What a silly mistake.  
  
We have **zero warnings** now in 5f5aa500d05515b68b677f085c542e18daba8bb7!   🥳  
  
I had been removing all the files the build script generates in the doc directory over and over before running `b2` but I did forget about `<boost root>/bin.v2/libs/utility`. It's kind of annoying `b2` doesn't detect the file changes as CMake does.

---

## Comment 79

> Username: glenfe  
> Created_at: 2021-11-16 05:30:01 UTC  
> Updated_at: 2021-11-16 05:31:59 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-969876456  

Excellent - just one issue left that I mentioned to you on Slack (regarding the file names of some of the reference documentation - e.g. the template specializations of call_traits) and this looks good to go.  
  
It should be possible to specify an id that results in a better filename, e.g.   instead of `html/boost/call_tra_idm45376720888496.html` it becomes something like `html/boost/call_traits_t_n.html` or `html/boost/call_traits_bounded_array.html`

---

## Comment 80

> Username: alandefreitas  
> Created_at: 2021-11-16 05:44:08 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-969881951  

> It should be possible to specify an id that results in a better filename, e.g. instead of html/boost/call_tra_idm45376720888496.html it becomes something like html/boost/call_traits_t_n.html or html/boost/call_traits_bounded_array.html  
  
Is this possible? 😀  
  
These filenames were bothering me a lot because they are not only ugly but they also use a new random number every time. But I just assumed that's how things are with Doxygen.

---

## Comment 81

> Username: glenfe  
> Created_at: 2021-11-16 13:36:27 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-970281996  

Maybe not, and I'm just imagining doxygen to be better than it actually is.

---

## Comment 82

> Username: alandefreitas  
> Created_at: 2021-11-16 13:38:23 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-970283567  

> Maybe not, and I'm just imagining doxygen to be better than it actually is.  
  
Oh... 😆

---

## Comment 83

> Username: Kojoley  
> Created_at: 2021-11-22 13:44:39 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-975538485  

```  
In file included from ../../../../boost/compressed_pair.hpp:18:  
../../../../boost/detail/compressed_pair.hpp:405:8: error: extra tokens at end of #endif directive [-Werror,-Wextra-tokens]  
#endif BOOST_UTILITY_DOCS  
       ^  
       //  
1 error generated.  
```

---

## Comment 84

> Username: vinniefalco  
> Created_at: 2021-11-22 14:55:22 UTC  
> Url: https://github.com/boostorg/utility/pull/79#issuecomment-975610079  

congrats :)

---
