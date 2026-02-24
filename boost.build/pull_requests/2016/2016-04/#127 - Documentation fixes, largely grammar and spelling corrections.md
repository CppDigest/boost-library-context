# #127 Documentation fixes, largely grammar and spelling corrections. [Merged]

> Username: eldiener  
> Created at: 2016-04-29 12:11:33 UTC  
> Updated at: 2021-10-02 22:19:12 UTC  
> Merged at: 2016-05-17 13:15:14 UTC  
> Closed at: 2016-05-17 13:15:14 UTC  
> Url: https://github.com/boostorg/build/pull/127  



---

## Comment 1

> Username: swatanabe  
> Created_at: 2016-04-29 14:22:07 UTC  
> Updated_at: 2016-04-30 16:59:42 UTC  
> Url: https://github.com/boostorg/build/pull/127#discussion_r61584900  

The original version is correct.  bin.v2 is specified by the superproject's Jamroot.

---

## Comment 2

> Username: swatanabe  
> Created_at: 2016-04-29 14:32:45 UTC  
> Updated_at: 2016-04-30 16:59:42 UTC  
> Url: https://github.com/boostorg/build/pull/127#discussion_r61586639  

compileflags is not a feature.  What you're thinking of is the use of compileflags here: http://www.boost.org/build/doc/html/bbv2/reference/tools.html, which is different.  I realize that this is confusing, but the existing documentation does describe the current behavior correctly.

---

## Comment 3

> Username: swatanabe  
> Created_at: 2016-04-29 14:34:44 UTC  
> Updated_at: 2016-04-30 16:59:42 UTC  
> Url: https://github.com/boostorg/build/pull/127#discussion_r61586982  

s/if/whether/ would be a more accurate rendering of the original intent of this sentence.

---

## Comment 4

> Username: swatanabe  
> Created_at: 2016-04-29 14:36:27 UTC  
> Updated_at: 2016-04-30 16:59:42 UTC  
> Url: https://github.com/boostorg/build/pull/127#discussion_r61587253  

Again, bin.v2 is specific to Boost.  The generic default is bin.

---

## Comment 5

> Username: swatanabe  
> Created_at: 2016-04-29 14:39:02 UTC  
> Updated_at: 2016-04-30 16:59:42 UTC  
> Url: https://github.com/boostorg/build/pull/127#discussion_r61587647  

The original formulation makes more sense.

---

## Comment 6

> Username: eldiener  
> Created_at: 2016-04-29 16:41:23 UTC  
> Updated_at: 2016-04-30 16:59:42 UTC  
> Url: https://github.com/boostorg/build/pull/127#discussion_r61606666  

I do not see how writing that 'cflags' applies to both C and C++ can be correct. Clearly this is not the case for any compiler toolset that is documented. Leaving that in the documentation is just a cause for confusion. It had certainly confused me until I realized it wasn't tthe case for any compiler toolset which is documented in Boost Build.

---

## Comment 7

> Username: eldiener  
> Created_at: 2016-04-29 16:44:53 UTC  
> Updated_at: 2016-04-30 16:59:42 UTC  
> Url: https://github.com/boostorg/build/pull/127#discussion_r61607143  

I disagree. The phrase 'added-to in subprojects' cannot be correct English while the phrase 'added-to subprojects' is the correct terminology.

---

## Comment 8

> Username: eldiener  
> Created_at: 2016-04-29 16:45:17 UTC  
> Updated_at: 2016-04-30 16:59:42 UTC  
> Url: https://github.com/boostorg/build/pull/127#discussion_r61607196  

I will make the correction.

---

## Comment 9

> Username: eldiener  
> Created_at: 2016-04-29 16:47:58 UTC  
> Updated_at: 2016-04-30 16:59:42 UTC  
> Url: https://github.com/boostorg/build/pull/127#discussion_r61607532  

I see now that you are correct and that 'specified whether produced object files...' etc. is the correct terminology. I will make the change.

---

## Comment 10

> Username: eldiener  
> Created_at: 2016-04-29 16:48:15 UTC  
> Updated_at: 2016-04-30 16:59:42 UTC  
> Url: https://github.com/boostorg/build/pull/127#discussion_r61607570  

I will make the change.

---

## Comment 11

> Username: swatanabe  
> Created_at: 2016-04-29 16:54:14 UTC  
> Updated_at: 2016-04-30 16:59:42 UTC  
> Url: https://github.com/boostorg/build/pull/127#discussion_r61608332  

AMDG  
  
On 04/29/2016 10:44 AM, Edward Diener wrote:  
  
> > @@ -280,8 +280,8 @@ top/  
> >        in their requirements, too.  Of course, any project can add  
> >        include paths to those specified by its parents. <footnote>  
> >        <para>Many  
> > -      features will be overridden,  
> > -      rather than added-to, in subprojects.  See <xref  
> > -      features will be overridden in,  
> > -      rather than added-to, subprojects.  See <xref  
>   
> I disagree. The phrase 'added-to in subprojects' cannot be correct English while the phrase 'added-to subprojects' is the correct terminology.  
  
It's the features that are being added-to, not the subproject.  
  
In Christ,  
Steven Watanabe

---

## Comment 12

> Username: swatanabe  
> Created_at: 2016-04-29 16:57:29 UTC  
> Updated_at: 2016-04-30 16:59:42 UTC  
> Url: https://github.com/boostorg/build/pull/127#discussion_r61609254  

AMDG  
  
On 04/29/2016 10:41 AM, Edward Diener wrote:  
  
> > @@ -561,13 +561,15 @@ path-constant DATA : data/a.txt ;  
> >        <varlistentry>  
> >          <term><literal>cflags</literal></term>  
> >          <term><literal>cxxflags</literal></term>  
> > -        <term><literal>compileflags</literal></term>  
>   
> I do not see how writing that 'cflags' applies to both C and C++ can be correct.  
  
Sadly it is.  
  
In Christ,  
Steven Watanabe

---

## Comment 13

> Username: eldiener  
> Created_at: 2016-04-29 17:09:29 UTC  
> Updated_at: 2016-04-30 16:59:42 UTC  
> Url: https://github.com/boostorg/build/pull/127#discussion_r61611958  

It's perfectly valid in the main clause to say:  
`Many features will be overridden in subprojects.`  
but saying in the alternative clause:  
`Many features will be added-to in subprojects`  
is not as correct as saying:  
`Many features will be added-to subprojects.`  
  
If you do not see this I will change it back and not try to correct it.

---

## Comment 14

> Username: eldiener  
> Created_at: 2016-04-29 17:14:17 UTC  
> Updated_at: 2016-04-30 16:59:42 UTC  
> Url: https://github.com/boostorg/build/pull/127#discussion_r61613534  

I will remove the change but I strongly feel that the documentation, even if correct in general, will just serve to lead end-users of Boost Build astray. At the very least a further explanation should be added directing the end-user to look at the toolset for his compiler where the meaning of 'cflag' often will be different.

---

## Comment 15

> Username: eldiener  
> Created_at: 2016-04-29 19:18:57 UTC  
> Updated_at: 2016-04-30 16:59:42 UTC  
> Url: https://github.com/boostorg/build/pull/127#discussion_r61631582  

I do not understand how 'cflags' can mean command line options for both C and C++ when the various compiler toolsets clearly state that 'cflags' specifies command line options for C mode and that 'compileflags' specifies command line options for both C and C++ mode. Can you explain this to me ?

---

## Comment 16

> Username: swatanabe  
> Created_at: 2016-04-29 19:25:47 UTC  
> Updated_at: 2016-04-30 16:59:42 UTC  
> Url: https://github.com/boostorg/build/pull/127#discussion_r61632411  

AMDG  
  
On 04/29/2016 11:14 AM, Edward Diener wrote:  
  
> > @@ -561,13 +561,15 @@ path-constant DATA : data/a.txt ;  
> >        <varlistentry>  
> >          <term><literal>cflags</literal></term>  
> >          <term><literal>cxxflags</literal></term>  
> > -        <term><literal>compileflags</literal></term>  
>   
> I will remove the change but I strongly feel that the documentation, even if correct in general, will just serve to lead end-users of Boost Build astray. At the very least a further explanation should be added directing the end-user to look at the toolset for his compiler where the meaning of 'cflag' often will be different.  
  
  No that's wrong.  The meaning of cflags as a  
feature is consistent.  cflags does apply to  
both C and C++ for all toolsets.  I checked  
both msvc and gcc:  
  
toolset.flags gcc.compile USER_OPTIONS <cflags> ;  
toolset.flags msvc.compile OPTIONS <cflags> : ;  
  
In Christ,  
Steven Watanabe

---

## Comment 17

> Username: swatanabe  
> Created_at: 2016-04-29 19:34:02 UTC  
> Updated_at: 2016-04-30 16:59:42 UTC  
> Url: https://github.com/boostorg/build/pull/127#discussion_r61633359  

AMDG  
  
On 04/29/2016 01:18 PM, Edward Diener wrote:  
  
> > @@ -561,13 +561,15 @@ path-constant DATA : data/a.txt ;  
> >        <varlistentry>  
> >          <term><literal>cflags</literal></term>  
> >          <term><literal>cxxflags</literal></term>  
> > -        <term><literal>compileflags</literal></term>  
>   
> I do not understand how 'cflags' can mean command line options for both C and C++ when the various compiler toolsets clearly state that 'cflags' specifies command line options for C mode and that 'compileflags' specifies command line options for both C and C++ mode. Can you explain this to me ?  
  
  You're confusing cflags as an option for  
toolset initialization with cflags as a feature.  
The arguments for 'using xxx' are toolset specific  
and are essentially named parameters.  They are  
completely independent from the feature/property  
framework, even though they share the same syntax.  
  
In Christ,  
Steven Watanabe

---

## Comment 18

> Username: eldiener  
> Created_at: 2016-04-29 19:47:23 UTC  
> Updated_at: 2016-04-30 16:59:42 UTC  
> Url: https://github.com/boostorg/build/pull/127#discussion_r61634904  

OK, I understand the distinction now, and where my previous confusion occurred.  
  
Having the exact same feature name meaning different things depending on where it is used seems to me a really bad programming design decision for Boost Build.  
  
I will remove the change I made, realizing it is wrong.

---

## Comment 19

> Username: eldiener  
> Created_at: 2016-05-02 20:40:31 UTC  
> Url: https://github.com/boostorg/build/pull/127#issuecomment-216356685  

Is there any objections to pushing these documentation fixes now ?

---

## Comment 20

> Username: vprus  
> Created_at: 2016-05-17 09:28:30 UTC  
> Url: https://github.com/boostorg/build/pull/127#issuecomment-219665891  

Steven, do you have any further comments?

---

## Comment 21

> Username: swatanabe  
> Created_at: 2016-05-17 12:38:08 UTC  
> Url: https://github.com/boostorg/build/pull/127#issuecomment-219704748  

AMDG  
  
On 05/17/2016 03:28 AM, Vladimir Prus wrote:  
  
> Steven, do you have any further comments?  
  
No, I think it's good.  
  
In Christ,  
Steven Watanabe

---

## Comment 22

> Username: vprus  
> Created_at: 2016-05-17 13:15:35 UTC  
> Url: https://github.com/boostorg/build/pull/127#issuecomment-219713728  

I've merged this pull request, thanks!

---
