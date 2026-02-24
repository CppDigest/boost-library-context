# #35 - min() macro conflict [Closed]

> Username: OlafvdSpek  
> Created at: 2018-10-13 17:32:46 UTC  
> Updated at: 2018-10-17 14:37:08 UTC  
> Closed at: 2018-10-17 14:37:08 UTC  
> Url: https://github.com/boostorg/convert/issues/35  

```  
strtol.hpp:  
ch_type  str[sz] = {0}; std::strncpy(str, &*range.begin(), std::min(sz - 1, range.size()));  
  
// boost\convert\strtol.hpp(209): error C2589: '(': illegal token on right side of '::'  
```  
  
I think this isn't properly guarded against Windows's macro named min.   
MSVC 15.8

---

## Comment 1

> Username: yet-another-user  
> Created at: 2018-10-13 20:09:22 UTC  
> Url: https://github.com/boostorg/convert/issues/35#issuecomment-429571487  

Yes, I believe you are correct suspecting the macro. Uh, the 'min' macro...  
it brings so many "wonderful" memories. :-) It's not just Windows. The X11  
windowing system still has it in X11/Xlibint.h...  
  
That said, I am not sure if humble boost\convert\strtol.hpp is the place to  
address the monumental clash of the OS macro and the C++ function. It  
appears that such a fundamental problem might need to be addressed on a  
higher level (and I expect it has) so that the user might need to include  
some "compatibility.hpp" or "portability.hpp" in their code. For example,  
on Linux I see the following done on the language levels:  
  
/usr/include >> grep -r 'undef min' *  
x86_64-linux-gnu/c++/7/bits/c++config.h:#undef min  
x86_64-linux-gnu/sys/sysmacros.h:#undef minor  
  
I'd expect to find something similar on Windows. What do you think?  
  
  
  
  
  
  
On Sun, Oct 14, 2018 at 4:32 AM Olaf van der Spek <notifications@github.com>  
wrote:  
  
> strtol.hpp:  
> ch_type  str[sz] = {0}; std::strncpy(str, &*range.begin(), std::min(sz - 1, range.size()));  
>  
> // boost\convert\strtol.hpp(209): error C2589: '(': illegal token on right side of '::'  
>  
> I think this isn't properly guarded against Windows's macro named min.  
> MSVC 15.8  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/convert/issues/35>, or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/ABswINNytUbSXB0ISKFk0LdlBcXlHnPaks5ukiO-gaJpZM4XavH9>  
> .  
>

---

## Comment 2

> Username: pabristow  
> Created at: 2018-10-15 09:42:05 UTC  
> Url: https://github.com/boostorg/convert/issues/35#issuecomment-429776989  

From: Mike [mailto:notifications@github.com]  
Sent: 13 October 2018 21:09  
To: boostorg/convert  
Cc: Subscribed  
Subject: Re: [boostorg/convert] min() macro conflict (#35)  
  
Yes, I believe you are correct suspecting the macro. Uh, the 'min' macro...  
it brings so many "wonderful" memories. :-) It's not just Windows. The X11  
windowing system still has it in X11/Xlibint.h...  
  
That said, I am not sure if humble boost\convert\strtol.hpp is the place to  
address the monumental clash of the OS macro and the C++ function. It  
appears that such a fundamental problem might need to be addressed on a  
higher level (and I expect it has) so that the user might need to include  
some "compatibility.hpp" or "portability.hpp" in their code. For example,  
on Linux I see the following done on the language levels:  
  
/usr/include >> grep -r 'undef min' *  
x86_64-linux-gnu/c++/7/bits/c++config.h:#undef min  
x86_64-linux-gnu/sys/sysmacros.h:#undef minor  
  
I'd expect to find something similar on Windows. What do you think?  
  
Isn’t the common way to overcome this by using brackets like this  
  
  (std::numeric_limits<result_type>::max)()  
  
so in this collection of hints  
  
https://svn.boost.org/trac10/wiki/Guidelines/WarningsGuidelines  
  
has this suggestion  
  
/DNOMINMAX - Don't define min/max macros (usually only applies if windows.h is involved).  
  
(Note that suitable bracketing, for example, (std::numeric_limits<result_type>::max)(), will avoid clashes with min/max macros, and is required to avoid being named'n'shamed by the Boost inspection program. So it may be better to check that clashes are correctly avoided by bracketing because others may use when min/max macros are defined rather than using this compiler option.)  
  
HTH  
  
Paul  
  
---  
Paul A. Bristow  
Prizet Farmhouse  
Kendal UK LA8 8AB  
+44 (0) 1539 561830  
  
  
  
  
On Sun, Oct 14, 2018 at 4:32 AM Olaf van der Spek <notifications@github.com>  
wrote:  
  
> strtol.hpp:  
> ch_type str[sz] = {0}; std::strncpy(str, &*range.begin(), std::min(sz - 1, range.size()));  
>  
> // boost\convert\strtol.hpp(209): error C2589: '(': illegal token on right side of '::'  
>  
> I think this isn't properly guarded against Windows's macro named min.  
> MSVC 15.8  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/convert/issues/35>, or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/ABswINNytUbSXB0ISKFk0LdlBcXlHnPaks5ukiO-gaJpZM4XavH9>  
> .  
>  
  
  
—  
You are receiving this because you are subscribed to this thread.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/convert/issues/35#issuecomment-429571487>, or mute the thread<https://github.com/notifications/unsubscribe-auth/ABBuAVxn2ycp355HFU68ITBhM02A-FqAks5ukkhygaJpZM4XavH9>.

---

## Comment 3

> Username: yet-another-user  
> Created at: 2018-10-15 10:01:37 UTC  
> Url: https://github.com/boostorg/convert/issues/35#issuecomment-429783996  

Paul, thank you for chiming in... and you are right of course... I suspect  
I still have something like that somewhere in my code... How could I  
forget?.. Will put the fix in... Or, maybe, just wait about 10 years for  
Windows to die naturally... :-) Thanks again. Much appreciated.  
  
On Mon, Oct 15, 2018 at 8:42 PM Paul A. Bristow <notifications@github.com>  
wrote:  
  
>  
>  
> From: Mike [mailto:notifications@github.com]  
> Sent: 13 October 2018 21:09  
> To: boostorg/convert  
> Cc: Subscribed  
> Subject: Re: [boostorg/convert] min() macro conflict (#35)  
>  
> Yes, I believe you are correct suspecting the macro. Uh, the 'min'  
> macro...  
> it brings so many "wonderful" memories. :-) It's not just Windows. The X11  
> windowing system still has it in X11/Xlibint.h...  
>  
> That said, I am not sure if humble boost\convert\strtol.hpp is the place  
> to  
> address the monumental clash of the OS macro and the C++ function. It  
> appears that such a fundamental problem might need to be addressed on a  
> higher level (and I expect it has) so that the user might need to include  
> some "compatibility.hpp" or "portability.hpp" in their code. For example,  
> on Linux I see the following done on the language levels:  
>  
> /usr/include >> grep -r 'undef min' *  
> x86_64-linux-gnu/c++/7/bits/c++config.h:#undef min  
> x86_64-linux-gnu/sys/sysmacros.h:#undef minor  
>  
> I'd expect to find something similar on Windows. What do you think?  
>  
> Isn’t the common way to overcome this by using brackets like this  
>  
> (std::numeric_limits<result_type>::max)()  
>  
> so in this collection of hints  
>  
> https://svn.boost.org/trac10/wiki/Guidelines/WarningsGuidelines  
>  
> has this suggestion  
>  
> /DNOMINMAX - Don't define min/max macros (usually only applies if  
> windows.h is involved).  
>  
> (Note that suitable bracketing, for example,  
> (std::numeric_limits<result_type>::max)(), will avoid clashes with min/max  
> macros, and is required to avoid being named'n'shamed by the Boost  
> inspection program. So it may be better to check that clashes are correctly  
> avoided by bracketing because others may use when min/max macros are  
> defined rather than using this compiler option.)  
>  
> HTH  
>  
> Paul  
>  
> ---  
> Paul A. Bristow  
> Prizet Farmhouse  
> Kendal UK LA8 8AB  
> +44 (0) 1539 561830  
>  
>  
>  
>  
> On Sun, Oct 14, 2018 at 4:32 AM Olaf van der Spek <  
> notifications@github.com>  
> wrote:  
>  
> > strtol.hpp:  
> > ch_type str[sz] = {0}; std::strncpy(str, &*range.begin(), std::min(sz -  
> 1, range.size()));  
> >  
> > // boost\convert\strtol.hpp(209): error C2589: '(': illegal token on  
> right side of '::'  
> >  
> > I think this isn't properly guarded against Windows's macro named min.  
> > MSVC 15.8  
> >  
> > —  
> > You are receiving this because you are subscribed to this thread.  
> > Reply to this email directly, view it on GitHub  
> > <https://github.com/boostorg/convert/issues/35>, or mute the thread  
> > <  
> https://github.com/notifications/unsubscribe-auth/ABswINNytUbSXB0ISKFk0LdlBcXlHnPaks5ukiO-gaJpZM4XavH9>  
>  
> > .  
> >  
>  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub<  
> https://github.com/boostorg/convert/issues/35#issuecomment-429571487>, or  
> mute the thread<  
> https://github.com/notifications/unsubscribe-auth/ABBuAVxn2ycp355HFU68ITBhM02A-FqAks5ukkhygaJpZM4XavH9>.  
>  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/convert/issues/35#issuecomment-429776989>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/ABswIOYRR4oJuWrxSflJinvPRJomvGxqks5ulFhugaJpZM4XavH9>  
> .  
>

---

## Comment 4

> Username: yet-another-user  
> Created at: 2018-10-15 21:49:12 UTC  
> Url: https://github.com/boostorg/convert/issues/35#issuecomment-430026689  

Olaf, I worked around the issue as per Paul's comments. I did not test it on MS Win (don't have it) but expect it all to be good. Please see if that is to your satisfaction. Tnx for pointing it out.

---

## Comment 5

> Username: OlafvdSpek  
> Created at: 2018-10-16 08:13:07 UTC  
> Url: https://github.com/boostorg/convert/issues/35#issuecomment-430143590  

Thanks!  
  
BTW, boost::cnv::strtol::str_to_i() might benefit from an assert(!range.empty()) as it does an unchecked *range.begin().

---

## Comment 6

> Username: pabristow  
> Created at: 2018-10-16 08:45:14 UTC  
> Url: https://github.com/boostorg/convert/issues/35#issuecomment-430153502  

From: Mike [mailto:notifications@github.com]  
Sent: 15 October 2018 22:49  
To: boostorg/convert  
Cc: Paul A. Bristow; Comment  
Subject: Re: [boostorg/convert] min() macro conflict (#35)  
  
Olaf, I worked around the issue as per Paul's comments. I did not test it on MS Win (don't have it) but expect it all to be good. Please see if that is to your satisfaction. Tnx for pointing it out.  
  
I’m confident that you have it sorted.  
  
Supplementary comment shutting the stable door after the horse has bolted ;-)  
  
You would have caught this, and many other potential problems with the Boost.Inspect program  
  
https://www.boost.org/doc/libs/release/tools/inspect/index.html  
  
The unbracketed min and max are tested by this bit of rite-only regexery  
  
I:\modular-boost\tools\inspect\minmax_check.cpp  
  
You need to build the tool using the jamfile, may sure it is ‘visible’ everywhere, and then run it from the /convert folder  
  
HTH too.  
  
Paul

---

## Comment 7

> Username: yet-another-user  
> Created at: 2018-10-16 10:22:02 UTC  
> Url: https://github.com/boostorg/convert/issues/35#issuecomment-430184367  

Thanks, Paul, for the pointer. Much appreciated. I'll keep your notes for  
the next "horse" and try using the tools mentioned... Jeez, Boost is such a  
huge trove of bits and pieces and tool and nuggets and everything...  
Sometimes it feels like it is slowly sinking under its own weight. :-( Or,  
maybe, I am just an old pessimist. Hope it is the latter.  
  
On Tue, Oct 16, 2018 at 7:45 PM Paul A. Bristow <notifications@github.com>  
wrote:  
  
>  
>  
> From: Mike [mailto:notifications@github.com]  
> Sent: 15 October 2018 22:49  
> To: boostorg/convert  
> Cc: Paul A. Bristow; Comment  
> Subject: Re: [boostorg/convert] min() macro conflict (#35)  
>  
> Olaf, I worked around the issue as per Paul's comments. I did not test it  
> on MS Win (don't have it) but expect it all to be good. Please see if that  
> is to your satisfaction. Tnx for pointing it out.  
>  
> I’m confident that you have it sorted.  
>  
> Supplementary comment shutting the stable door after the horse has bolted  
> ;-)  
>  
> You would have caught this, and many other potential problems with the  
> Boost.Inspect program  
>  
> https://www.boost.org/doc/libs/release/tools/inspect/index.html  
>  
> The unbracketed min and max are tested by this bit of rite-only regexery  
>  
> I:\modular-boost\tools\inspect\minmax_check.cpp  
>  
> You need to build the tool using the jamfile, may sure it is ‘visible’  
> everywhere, and then run it from the /convert folder  
>  
> HTH too.  
>  
> Paul  
>  
>  
>  
>  
>  
>  
>  
>  
>  
>  
> —  
> You are receiving this because you were assigned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/convert/issues/35#issuecomment-430153502>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/ABswIIcgnYLHnC2DlhRHZHBnOx6VPFRVks5ulZyjgaJpZM4XavH9>  
> .  
>

---

## Comment 8

> Username: yet-another-user  
> Created at: 2018-10-16 22:16:06 UTC  
> Url: https://github.com/boostorg/convert/issues/35#issuecomment-430420404  

Olaf,  
> BTW, boost::cnv::strtol::str_to_i() might benefit from an  
> assert(!range.empty()) as it does an unchecked *range.begin()  
I just checked the piece you mentioned and, yes, if the function was  public, the added assert() might be beneficial... although I'd think a simple return would be more appropriate... and that is exactly what the caller of that function -- cnvbase::str_to_(). Agree?

---

## Comment 9

> Username: OlafvdSpek  
> Created at: 2018-10-17 14:37:08 UTC  
> Url: https://github.com/boostorg/convert/issues/35#issuecomment-430654540  

I did see the caller and it's just a minor thing.. point is since it's a function, it could also be called from elsewhere.
