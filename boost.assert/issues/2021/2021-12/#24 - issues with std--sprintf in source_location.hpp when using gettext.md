# #24 - issues with std::sprintf in source_location.hpp when using gettext [Closed]

> Username: mclegrand  
> Created at: 2021-12-17 14:13:35 UTC  
> Updated at: 2021-12-22 15:15:21 UTC  
> Closed at: 2021-12-22 15:15:05 UTC  
> Url: https://github.com/boostorg/assert/issues/24  

Hi!   
  
Hi! libintl (part of gettext) #defines sprintf into libintl_sprintf (cf https://github.com/autotools-mirror/gettext/blob/c438dbf70dd293fa099e94d8fb964cd133675b5f/gettext-runtime/intl/libgnuintl.in.h#L394 - since 19 years ago    
apparently), which creates compilation issues with latest boost, because since  
https://github.com/boostorg/assert/commit/1f98fab0bfecc5fe3999fa96d396cf7155d43022#diff there is a `std::sprintf` which creates compilation errors in localized programs using boost on mingw64. This in particular affects Inkscape because of course 'libintl_sprintf' is not a member of 'std' (e.g. https://gitlab.com/inkscape/inkscape/-/jobs/1893141019 ) …   
  
is there a way that you could remove the std:: in boost assert ?   
  
Thanks :)

---

## Comment 1

> Username: pdimov  
> Created at: 2021-12-17 14:21:10 UTC  
> Url: https://github.com/boostorg/assert/issues/24#issuecomment-996759017  

That's horrible.

---

## Comment 2

> Username: mclegrand  
> Created at: 2021-12-17 14:29:32 UTC  
> Url: https://github.com/boostorg/assert/issues/24#issuecomment-996765060  

I don't disagree with that ^^   
  
I was not aware of that `#define` until from my maintainer point of view "a boost update broke the build" … maybe there is a way to get gettext to do things differently but "not breaking that 20yo i18n code for someone" seemed harder that asking here if there would be a way to "not use std::sprintf" literally (I could do both, of course)

---

## Comment 3

> Username: pdimov  
> Created at: 2021-12-17 14:40:17 UTC  
> Url: https://github.com/boostorg/assert/issues/24#issuecomment-996772610  

You should be able to make this work by inserting  
```  
#include <boost/assert/source_location.hpp>  
```  
as the first include in the failing source files, before `<glibmm/i18n.h>` has the chance to do its evil thing.

---

## Comment 4

> Username: pdimov  
> Created at: 2021-12-17 14:47:40 UTC  
> Url: https://github.com/boostorg/assert/issues/24#issuecomment-996778022  

Although on second thought, if you support compilation under older Boost versions, `source_location.hpp` is a new header and the include will fail. One option is to use `#include <boost/throw_exception.hpp>` instead - it includes `<boost/assert/source_location.hpp>` in new Boost versions, when it's present, but doesn't in the old ones when it isn't.

---

## Comment 5

> Username: mclegrand  
> Created at: 2021-12-22 15:15:05 UTC  
> Updated at: 2021-12-22 15:15:21 UTC  
> Url: https://github.com/boostorg/assert/issues/24#issuecomment-999650045  

I reached out to gettext and suggested a patch to add libintl_sprintf into the std namespace, which got accepted, so I guess this problem will disappear in the next gettext version ( https://lists.gnu.org/archive/html/bug-gettext/2021-12/msg00029.html )  
  
Thanks for the answers!
