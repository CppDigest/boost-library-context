# #24 Fix duplicate equal signs in Doxygen-generated enum value initializers [Merged]

> Username: Lastique  
> Created at: 2024-04-28 12:27:59 UTC  
> Updated at: 2024-05-13 12:31:34 UTC  
> Merged at: 2024-05-13 12:31:28 UTC  
> Closed at: 2024-05-13 12:31:28 UTC  
> Url: https://github.com/boostorg/boostbook/pull/24  

Doxygen 1.9.1 generates enum `<initializer>` tags with text starting with an equal sign ('=') followed by the enum value initializer. This sign was copied under the BoostBook `<default>` tag, and BoostBook styleseets add an equal sign of their own when converting this tag to HTML. This resulted in duplicate equal signs in the HTML output.  
  
This commit strips the leading equal sign from the initializer before putting it into the `<default>` tag.  
  
Before:  
  
![Screenshot_20240428_143057](https://github.com/boostorg/boostbook/assets/1589747/396625a1-23ee-48bd-9a1f-0dd316fab5bc)  
  
After:  
  
![Screenshot_20240428_151556](https://github.com/boostorg/boostbook/assets/1589747/f5e29856-5328-4b4f-8ce5-988189e22a42)

---

## Comment 1

> Username: Lastique  
> Created_at: 2024-05-05 12:05:22 UTC  
> Url: https://github.com/boostorg/boostbook/pull/24#issuecomment-2094780598  

@glenfe Please, take a look.

---

## Comment 2

> Username: Lastique  
> Created_at: 2024-05-12 15:50:10 UTC  
> Url: https://github.com/boostorg/boostbook/pull/24#issuecomment-2106297032  

@pdimov Could you take a look, please?

---

## Comment 3

> Username: pdimov  
> Created_at: 2024-05-12 15:59:24 UTC  
> Url: https://github.com/boostorg/boostbook/pull/24#issuecomment-2106299184  

I know nothing about XSLT, sorry.

---

## Comment 4

> Username: Lastique  
> Created_at: 2024-05-12 16:28:41 UTC  
> Url: https://github.com/boostorg/boostbook/pull/24#issuecomment-2106307231  

Is there someone who could review and merge these changes? Looks like Glen is no longer active, and I don't know who else has merge permissions.

---

## Comment 5

> Username: glenfe  
> Created_at: 2024-05-12 17:44:25 UTC  
> Url: https://github.com/boostorg/boostbook/pull/24#issuecomment-2106324500  

If we have nobody that can actually review these, then maybe you (@Lastique) can become a maintainer of boostbook and then merge yourself. This also means verifying the impact of these changes on any Boost libraries' docs that they affect.

---

## Comment 6

> Username: pdimov  
> Created_at: 2024-05-12 17:52:16 UTC  
> Url: https://github.com/boostorg/boostbook/pull/24#issuecomment-2106328687  

@grisumbras has been fixing things in https://github.com/boostorg/docca recently.

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2024-05-12 18:44:35 UTC  
> Url: https://github.com/boostorg/boostbook/pull/24#issuecomment-2106341179  

The problem is that there are no tests. What this repository needs is CI integration and some input cpp files which have the reference extracted via doxygen and rendered into a multi-page reference. The per-commit CI script should build the reference using the changes and then build an index of which HTML pages are different. A GitHub integration would then reply to the pull request showing a preview of what the changed HTML looks like.  
  
If building the reference fails then this needs to be noted in CI as well, and the GitHub integration should reply to the pull request noting that the build failed.

---

## Comment 8

> Username: vinniefalco  
> Created_at: 2024-05-12 19:11:48 UTC  
> Url: https://github.com/boostorg/boostbook/pull/24#issuecomment-2106347439  

I would maybe try building the docs for a couple of other Boost libraries whose references are build using these scripts, and make sure nothing has broken.

---

## Comment 9

> Username: grisumbras  
> Created_at: 2024-05-12 20:34:11 UTC  
> Url: https://github.com/boostorg/boostbook/pull/24#issuecomment-2106367163  

> @grisumbras has been fixing things in https://github.com/boostorg/docca recently.  
  
I've written a Python program that tries to mostly match the behaviour of Docca (modulo bugs). But I don't really know XSLT.

---

## Comment 10

> Username: Lastique  
> Created_at: 2024-05-13 10:27:35 UTC  
> Url: https://github.com/boostorg/boostbook/pull/24#issuecomment-2107210539  

> If we have nobody that can actually review these, then maybe you (@Lastique) can become a maintainer of boostbook and then merge yourself. This also means verifying the impact of these changes on any Boost libraries' docs that they affect.  
  
I have replied to Vinnie (and you) privately to the similar offer.  
  
To reiterate, I'm not very familiar with XSL or BoostBook/DocBook, but with googling around I'm able to do simple stuff like this one and the other PRs I made. I'd prefer if someone more knowledgeable than myself would become the maintainer, but if there is no such candidate, you can consider me a volunteer. I just can't promise heavy duty maintenance.

---

## Comment 11

> Username: glenfe  
> Created_at: 2024-05-13 11:48:58 UTC  
> Url: https://github.com/boostorg/boostbook/pull/24#issuecomment-2107366618  

@Lastique added you to the repository access list.   
  
No obligations or commitments needed beyond responsibility for any changes you make (like this fix).

---

## Comment 12

> Username: Lastique  
> Created_at: 2024-05-13 12:31:22 UTC  
> Url: https://github.com/boostorg/boostbook/pull/24#issuecomment-2107451943  

Thanks.

---
