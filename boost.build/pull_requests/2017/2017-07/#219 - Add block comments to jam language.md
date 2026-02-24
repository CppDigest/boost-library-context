# #219 Add block comments to jam language. [Merged]

> Username: grafikrobot  
> Created at: 2017-07-27 13:56:57 UTC  
> Updated at: 2021-10-02 22:08:45 UTC  
> Merged at: 2017-07-29 00:39:50 UTC  
> Closed at: 2017-07-29 00:39:50 UTC  
> Url: https://github.com/boostorg/build/pull/219  

This adds non-nested block comments wrapped with "#*" and "*#". This  
should help in cleaning up embedded documentation and generally make  
writing jam code easier.

---

## Comment 1

> Username: swatanabe  
> Created_at: 2017-07-27 14:11:35 UTC  
> Url: https://github.com/boostorg/build/pull/219#issuecomment-318373925  

AMDG  
  
On 07/27/2017 07:56 AM, Rene Rivera wrote:  
> This adds non-nested block comments wrapped with "#*" and "*#". This  
> should help in cleaning up embedded documentation and generally make  
> writing jam code easier.  
  
This seems somewhat dangerous:  
  
# * ... - this is common plain text formatting of a list  
#* ... - this is the beginning of a block comment  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: grafikrobot  
> Created_at: 2017-07-27 14:20:41 UTC  
> Url: https://github.com/boostorg/build/pull/219#issuecomment-318376553  

@swatanabe Good point.. Do you have a suggestion for another style to use? Some possibilities I can think of are (in order or preference) `#{ }#`, `#| |#`, `#/ /#`, `#: :#`, `#~ ~#`.

---

## Comment 3

> Username: frenchtoast747  
> Created_at: 2017-07-27 16:04:33 UTC  
> Updated_at: 2017-07-27 16:04:46 UTC  
> Url: https://github.com/boostorg/build/pull/219#issuecomment-318408102  

Would it be too difficult to incorporate Python's docstring as an entity?  
```  
local foo = bar ;  
"""  
this is foo. it does bar.  
"""  
```  
  
I'm assuming that in order to do that, though, it would require a semicolon and actually become a statement. And/or it would make the grammar too difficult to deal with.  
  
Side note, I have used action declarations as block comments:  
```  
actions {  
  hello there! This is just a raw string, so anything goes.  
}  
```  
But, I typically only do that in order to comment out chunks of code, rather than as comments.

---

## Comment 4

> Username: grafikrobot  
> Created_at: 2017-07-27 20:22:58 UTC  
> Updated_at: 2017-07-27 20:23:09 UTC  
> Url: https://github.com/boostorg/build/pull/219#issuecomment-318475821  

@frenchtoast747 It would be rather difficult. The comment handling happens during the token scanner which can only feed back one token. To implement that it would mean changing the grammar which is considerably more complicated matter.

---

## Comment 5

> Username: swatanabe  
> Created_at: 2017-07-27 22:01:03 UTC  
> Url: https://github.com/boostorg/build/pull/219#issuecomment-318498828  

AMDG  
  
On 07/27/2017 08:20 AM, Rene Rivera wrote:  
> @swatanabe Good point.. Do you have a suggestion for another style to use? Some possibilities I can think of are (in order or preference) `#{ }#`, `#| |#`, `#/ /#`, `#: :#`, `#~ ~#`.  
>   
  
  `#{` is a bad idea because it interferes with commented out code.  
`#:` has the same problem, though less so, since ':' rarely appears  
at the beginning of a line.  For `#~`, I think I've seen a number  
of .jam files with comments formatted like this.  I don't know why,  
however.  Is there any precedent in other languages that use `#`  
for comments?  
  Actually, I'm not really sure why you want this is the first place.  
I've never found the regular # comments to be particularly annoying.  
The main place where you need block comments is when documenting  
parameters inside the parameter list, but if you're putting comments  
there, you'll need line breaks anyway or the lines will get way  
too long.  
  
In Christ,  
Steven Watanabe

---

## Comment 6

> Username: grafikrobot  
> Created_at: 2017-07-28 01:54:06 UTC  
> Url: https://github.com/boostorg/build/pull/219#issuecomment-318534527  

You are close.. The use case I have in mind is for adding embedded documentation. In particular for tools like quickbook and acsiidoctor that support including documentation segments from "arbitrary" files. Even though we have the embedded "--help" system, it's cumbersome and it's rarely used. As people prefer to read documentation in HTML or PDF form instead. I'm trying to add the embedded documentation similar to how I document the Predef library. It's much easier to keep such documentation up to date and be more comprehensive. Without the block comments the tools need to have special handling for the commenting format. And I really want to avoid more custom tool work.

---

## Comment 7

> Username: swatanabe  
> Created_at: 2017-07-28 02:56:01 UTC  
> Url: https://github.com/boostorg/build/pull/219#issuecomment-318542832  

AMDG  
  
On 07/27/2017 07:54 PM, Rene Rivera wrote:  
> You are close.. The use case I have in mind is for adding embedded documentation. In particular for tools like quickbook and acsiidoctor that support including documentation segments from "arbitrary" files. Even though we have the embedded "--help" system, it's cumbersome and it's rarely used. As people prefer to read documentation in HTML or PDF form instead. I'm trying to add the embedded documentation similar to how I document the Predef library. It's much easier to keep such documentation up to date and be more comprehensive. Without the block comments the tools need to have special handling for the commenting format. And I really want to avoid more custom tool work.  
>   
  
  In theory, the existing help system can generate  
html, although I haven't actually tried it.  Also,  
# is one of the most widely used comment formats.  
I'm not convinced that creating a new unique form  
of comment is a good idea.  You can always preprocess  
the files with grep '^#`' (to match quickbook's //`)  
or some such.  
  
In Christ,  
Steven Watanabe

---

## Comment 8

> Username: grafikrobot  
> Created_at: 2017-07-28 03:13:50 UTC  
> Url: https://github.com/boostorg/build/pull/219#issuecomment-318545577  

PS. The `#~` Is probably my fault. At some point one of the text editors I used had that as the default line commenting style.  
  
Lets see comment styles with `#`..  
- Algol 68: `# BlockComment #`  
- Various shells: `<# BlockComment #>`  
- Curl: `|# BlockComment #|`  
- Cobra: `/# BlockComment #/`  
- Lisp/Scheme: `#| BlockComment |#`  
- Perl: #`{ BlockComment }`  
- Python: `''' BlockComment '''` `""" BlockComment """`  
- Seed7: `(* BlockComment *)`  
  
So maybe the Lisp/Scheme style? We could probably also do `<# #>`. But not starting with `#` seems risky in collisions with use inside regular tokens.

---

## Comment 9

> Username: grafikrobot  
> Created_at: 2017-07-28 03:32:38 UTC  
> Url: https://github.com/boostorg/build/pull/219#issuecomment-318548287  

On Thu, Jul 27, 2017 at 9:56 PM, swatanabe <notifications@github.com> wrote:  
  
> In theory, the existing help system can generate  
> html, although I haven't actually tried it.  
  
  
I've tried it. It works. I also have some local changes to have it generate  
Quickbook. But, it's clunky and inflexible. And it doesn't really help with  
documenting stuff outside of the general module/rules/classes stuff.  
  
  
> Also,  
> # is one of the most widely used comment formats.  
> I'm not convinced that creating a new unique form  
> of comment is a good idea. You can always preprocess  
> the files with grep '^#`' (to match quickbook's //`)  
> or some such.  
  
  
I could preprocess.. But that's yet another tooling layer to maintain.

---

## Comment 10

> Username: swatanabe  
> Created_at: 2017-07-28 23:35:21 UTC  
> Url: https://github.com/boostorg/build/pull/219#issuecomment-318784959  

AMDG  
  
On 07/27/2017 09:13 PM, Rene Rivera wrote:  
>   
> Lets see comment styles with `#`..  
> - Algol 68: `# BlockComment #`  
> - Various shells: `<# BlockComment #>`  
> - Curl: `|# BlockComment #|`  
> - Cobra: `/# BlockComment #/`  
> - Lisp/Scheme: `#| BlockComment |#`  
> - Perl: #`{ BlockComment }`  
> - Python: `''' BlockComment '''` `""" BlockComment """`  
  
Well, technically this is a string...  
  
> - Seed7: `(* BlockComment *)`  
>   
> So maybe the Lisp/Scheme style?  
  
+1.  
  
> We could probably also do `<# #>`. But not starting with `#` seems risky in collisions with use inside regular tokens.  
>   
  
  Yeah.  It wouldn't matter if we had a smarter tokenizer,  
but with the way things are, `#` can appear in the middle  
of a token.  
  
In Christ,  
Steven Watanabe

---
