# #31 Changes to get things linking correctly with msvc: [Merged]

> Username: jzmaddock  
> Created at: 2015-10-18 10:53:09 UTC  
> Updated at: 2015-10-19 17:02:54 UTC  
> Merged at: 2015-10-18 16:46:26 UTC  
> Closed at: 2015-10-18 16:46:26 UTC  
> Url: https://github.com/boostorg/serialization/pull/31  

1) Move the two facets utf8_codecvt_facet and codecvt_null into  
the main serialization archive as they get referenced from xml_oarchive.obj.  
2) Add DLL-interface to utf8_codecvt_facet.  
3) Change codecvt_null to use narrow character DLL interface macros.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2015-10-18 10:54:24 UTC  
> Url: https://github.com/boostorg/serialization/pull/31#issuecomment-149001254  

Note: This patch only addresses linkage issue with serialization in develop, it doesn't address the runtime crashes due to facet-lifetime issues discussed elsewhere.

---

## Comment 2

> Username: robertramey  
> Created_at: 2015-10-19 03:42:07 UTC  
> Url: https://github.com/boostorg/serialization/pull/31#issuecomment-149088753  

I merged this in without much thought but it doesn't seem to be working.  All the MSVC and gcc compilers are still failing.  We're still getting errors which look like calling non-visible functions.  
  
Take a look at codecvt_null.hpp  
  
You've changed the visibility of the specialization codecvt<wchar>  from BOOST_WARCHIVE_DECL to BOOST_ARCHIVE_DECL.  But this specialization is only invoked from wide character archive versions.  
  
Meanwhile the specialization codecvt_null<char> doesn't seem to be exported at all, but it's used by basic_binary_primitive - at least.  So it seems to me This specialization should be exported with BOOST_ARCHIVE_DECL.  
  
Do you feel confident that your changes are correct?  
  
Robert Ramey

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2015-10-19 08:17:15 UTC  
> Url: https://github.com/boostorg/serialization/pull/31#issuecomment-149139943  

On 19/10/2015 04:42, Robert Ramey wrote:  
  
> I merged this in without much thought but it doesn't seem to be   
> working. All the MSVC and gcc compilers are still failing. We're still   
> getting errors which look like calling non-visible functions.  
>   
> Double checked everything is linking OK (it didn't even link before),   
> but still crashes at runtime.  Will debug again and find out what's   
> going on.  
>   
> Take a look at codecvt_null.hpp  
>   
> You've changed the visibility of the specialization codecvt from   
> BOOST_WARCHIVE_DECL to BOOST_ARCHIVE_DECL. But this specialization is   
> only invoked from wide character archive versions.  
  
That's what I thought, but the lib wouldn't link unless that component   
was moved out of the wide char lib, and into the main one (hence the   
change to the Jamfile), from memory it was xml_oarchive.obj that complained.  
  
> Meanwhile the specialization codecvt_null doesn't seem to be exported   
> at all, but it's used by basic_binary_primitive - at least. So it   
> seems to me This specialization should be exported with   
> BOOST_ARCHIVE_DECL.  
  
Will check.  
  
John.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2015-10-19 08:44:41 UTC  
> Url: https://github.com/boostorg/serialization/pull/31#issuecomment-149147744  

On 19/10/2015 04:42, Robert Ramey wrote:  
  
> I merged this in without much thought but it doesn't seem to be   
> working. All the MSVC and gcc compilers are still failing. We're still   
> getting errors which look like calling non-visible functions.  
  
It's a lifetime issue the same as before - your restored   
basic_streambuf_locale_saver is restoring the locale in the stream   
buffer, but ios_base has a locale too and that is _not_ restored, so   
when the archive destructor is complete, reference count on the facet it   
contains is still 2.  You need to restore the locale to the _stream_,   
not just the stream buffer.  
  
> Take a look at codecvt_null.hpp  
>   
> You've changed the visibility of the specialization codecvt from   
> BOOST_WARCHIVE_DECL to BOOST_ARCHIVE_DECL. But this specialization is   
> only invoked from wide character archive versions.  
>   
> Meanwhile the specialization codecvt_null doesn't seem to be exported   
> at all, but it's used by basic_binary_primitive - at least. So it   
> seems to me This specialization should be exported with   
> BOOST_ARCHIVE_DECL.  
  
Which specialization?  The <char> specialization is all in the header,   
no need to export anything.  The <wchar_t> specialization has it's   
member functions exported (in point of fact it's probably easier/safer   
to mark the whole class as exported, but I didn't change that).  
  
HTH, John.

---

## Comment 5

> Username: robertramey  
> Created_at: 2015-10-19 16:13:26 UTC  
> Url: https://github.com/boostorg/serialization/pull/31#issuecomment-149262100  

> On Oct 19, 2015, at 1:44 AM, jzmaddock notifications@github.com wrote:  
>   
> On 19/10/2015 04:42, Robert Ramey wrote:  
>   
> > I merged this in without much thought but it doesn't seem to be   
> > working. All the MSVC and gcc compilers are still failing. We're still   
> > getting errors which look like calling non-visible functions.  
>   
> It's a lifetime issue the same as before - your restored   
> basic_streambuf_locale_saver is restoring the locale in the stream   
> buffer,  
  
correct  
  
> but ios_base has a locale too and that is _not_ restored, so   
  
well, the ios_base::imbue has never been changed.  
  
> when the archive destructor is complete, reference count on the facet it   
> contains is still 2.  
  
Hmmm - the local codecvt facet is intialized with a value of 1 so I would expect  
to be 1 when the archive destructor is invoked.  
  
> You need to restore the locale to the _stream_,   
> not just the stream buffer.  
  
even though i never changed it?  I only changed the streambuf one.  
  
I have to say I was totally surprised by the suggestion that there are TWO locales  
here - one in the stream and another in the streambuf (a member of the stream).  
  
I have always assumed that the ios::imbue(const & loc) just passed on the new   
locale to the member streambuf.  Looking at 27.5.3 and.3 27.6.3.4.1 doesn’t clarify  
things for me. I’m left mystified how this thing is expected to be used.  
  
Note that for binary archives - there is no stream.  We just the stream buffer directly  
to avoid invocation of extra overhead which isn’t necessary.  This is one of the   
motivations for the codecvt_null facet which is intended to avoid usage of any  
code conversion which couldn’t be useful in this context.  I’m suspecting that  
the std::codecvt<char, char> might do this - but I haven’t found any information on   
what it actually does - in any case it probably didn’t exist when codecvt_null was  
implemented.  
  
Of course this leaves one totally in the dark as to what happens when one sets locale  
on both streambuf and stream - (not that I did this).  Anyway….  
  
Looking into source for libstdc++ I DO see a  
protected member attribure_M_ios_locale .libstdc++: std::ios_base Class Reference https://gcc.gnu.org/onlinedocs/libstdc++/latest-doxygen/a00762.html  
I also see something similar in lib++ source code.  see  
‎llvm.org/svn/llvm-project/libcxx/trunk/include/ios http://llvm.org/svn/llvm-project/libcxx/trunk/include/ios  
  
> > Take a look at codecvt_null.hpp  
> >   
> > You've changed the visibility of the specialization codecvt from   
> > BOOST_WARCHIVE_DECL to BOOST_ARCHIVE_DECL. But this specialization is   
> > only invoked from wide character archive versions.  
> >   
> > Meanwhile the specialization codecvt_null doesn't seem to be exported   
> > at all, but it's used by basic_binary_primitive - at least. So it   
> > seems to me This specialization should be exported with   
> > BOOST_ARCHIVE_DECL.  
>   
> Which specialization? The <char> specialization is all in the header,   
> no need to export anything. The <wchar_t> specialization has it's   
> member functions exported (in point of fact it's probably easier/safer   
> to mark the whole class as exported, but I didn't change that).  
>   
> HTH, John.  
>   
> —  
> Reply to this email directly or view it on GitHub https://github.com/boostorg/serialization/pull/31#issuecomment-149147744.  
  
Robert Ramey  
Robert Ramey Software Development  
(805)569-3793  
ramey@rrsd.com  
www.rrsd.com

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2015-10-19 17:02:54 UTC  
> Url: https://github.com/boostorg/serialization/pull/31#issuecomment-149282527  

> > You need to restore the locale to the _stream_,  
> > not just the stream buffer.  
>   
> even though i never changed it? I only changed the streambuf one.  
>   
> In basic_text_oprimitive.ipp:95 you call imbue on the stream, but in the   
> classes destructor, you restore the locale to the stream's buffer.  
>   
> I have to say I was totally surprised by the suggestion that there are   
> TWO locales  
> here - one in the stream and another in the streambuf (a member of the   
> stream).  
>   
> I have always assumed that the ios::imbue(const & loc) just passed on   
> the new  
> locale to the member streambuf. Looking at 27.5.3 and.3 27.6.3.4.1   
> doesn’t clarify  
> things for me. I’m left mystified how this thing is expected to be used.  
>   
> I suspect you are not alone!  
  
However, in practice, since ios_base has imbue/getloc members, but no   
stream buffer, it must maintain it's own locale state?  
  
C++14 says of basic_ios::imbue:  
  
locale imbue(const locale& loc);  
Effects: Calls ios_base::imbue(loc) (27.5.3.3) and if rdbuf()!=0 then   
rdbuf()->pubimbue(loc) (27.6.3.2.1).  
  
Which I think settles it?  
  
> Note that for binary archives - there is no stream. We just the stream   
> buffer directly  
> to avoid invocation of extra overhead which isn’t necessary. This is   
> one of the  
> motivations for the codecvt_null facet which is intended to avoid   
> usage of any  
> code conversion which couldn’t be useful in this context. I’m   
> suspecting that  
> the std::codecvt<char, char> might do this - but I haven’t found any   
> information on  
> what it actually does - in any case it probably didn’t exist when   
> codecvt_null was  
> implemented.  
> In that case you would have to treat archives where you have only the   
> buffer differently to those where you have the stream.  
  
You could I suppose manipulate just the stream buffer locale and leave   
the stream unchanged, though exactly what happens when the the two   
locales are out of synch is anyone's guess.  I suggest you don't go   
there, in case it opens up a whole other can of worms.  
  
In fact I did wonder if this was the cause of your original bug report -   
before you create a facet on the heap, call basic_ios::imbue which then   
passes it on to both ios_base and the stream buffer, but on destruction   
you only reset the buffer's locale.  So now you're facet still exists,   
but only in the stream, not the buffer.  Potentially that leads to a   
dangling pointer if the serialization lib is unloaded before the stream   
is destroyed (as would happen with cout for example), and/or   
inconsistent behaviour due to the mixed locales?  
  
Best, John.

---
