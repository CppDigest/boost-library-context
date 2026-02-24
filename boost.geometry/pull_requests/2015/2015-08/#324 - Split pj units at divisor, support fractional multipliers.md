# #324 Split pj units at divisor, support fractional multipliers. [Merged]

> Username: jeremy-murphy  
> Created at: 2015-08-30 09:56:40 UTC  
> Updated at: 2015-10-17 08:42:58 UTC  
> Merged at: 2015-10-17 08:42:57 UTC  
> Closed at: 2015-10-17 08:42:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/324  

Is this what you had in mind, with regard to the TODO comment?

---

## Comment 1

> Username: awulkiew  
> Created_at: 2015-09-04 12:11:47 UTC  
> Updated_at: 2015-09-04 12:12:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/324#issuecomment-137718779  

Thanks for the PR! It looks good for me, besides that `atof` can return undefined value if the input is invalid. I'd consider using e.g. `boost::lexical_cast<>` throwing an exception. But this was also a problem in the original code.  
  
With merging we must wait for Barend because he's the one who's porting the code from Proj4 and contributing back. This is also why he prefers to maintain the lines count, so this addition could be implemented as a free function. If defined in a separate header file it'd be reusable too.  
  
And then instead of this:  
  
```  
pin.to_meter = atof(s.c_str());  
```  
  
there could simply be:  
  
```  
pin.to_meter = some_name(s);  
```  
  
But let's wait for Barend's response.  
  
Btw, I'm guessing it's not the only one place where this is needed, right? In case I was right, is it already implemented in other places or are there also TODOs there?

---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2015-09-16 04:43:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/324#issuecomment-140625471  

Sure, throwing an exception is almost certainly better. Not sure off the top of my head if there are locale issues with respect to the decimal point: presumably it would be sufficient to ensure that the conversion uses the C locale and not the user's locale?  
  
I think this is the only place that the conversion is needed, so I'm not sure what the issue of maintaining line count is about?

---

## Comment 3

> Username: awulkiew  
> Created_at: 2015-10-08 18:38:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/324#issuecomment-146648468  

Regarding the lines count. This code is a port of Proj4. It's simply convenient to have the algorithm looking exactly the same in Geometry's extensions and the original Proj4. So when the lines of the code of both libraries are traversed one by one it's easy to find correspondence. I can imagine that this simplifies the synchronization of both libraries in the case of updating Boost.Geometry from Proj4 and also contributing the bugfixes back to Proj4. AFAIR this is also what Barend have said in the past.  
  
In this particular line the string is parsed and the number assigned. If the suggestion mentioned above was implemented (parsing function called) the code in both libraries would be very similar, with the only difference that in Boost.Geometry the string would be parsed differently.  
  
I also know that Barend is currently working on projections. Barend would this change interfere with your work?

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2015-10-08 21:07:37 UTC  
> Updated_at: 2015-10-11 12:36:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/324#discussion_r41569090  

Please at {} in every if

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2015-10-08 21:08:05 UTC  
> Updated_at: 2015-10-11 12:36:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/324#discussion_r41569150  

Please check for denominator=0

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2015-10-08 21:08:57 UTC  
> Updated_at: 2015-10-11 12:36:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/324#discussion_r41569263  

Actually check for to_meter=0 should be done here too (not related to your changes)

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2015-10-08 21:10:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/324#issuecomment-146685996  

I'm OK with merging. Thanks for the contribution.  
@awulkiew  is right  that this all should stay close to the original. But the split was skipped at the time I ported it, and this change makes it closer to the original.   
Some of the files (as this one, all in impl) are manually converted. The rest (in proj) is automatically converted and should stay untouched.

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2015-10-08 21:13:51 UTC  
> Updated_at: 2015-10-11 12:36:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/324#discussion_r41569841  

Maybe divisor is not the most clear name for this variable. Just pos like they are usually called?

---

## Comment 9

> Username: jeremy-murphy  
> Created_at: 2015-10-08 23:05:51 UTC  
> Updated_at: 2015-10-11 12:36:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/324#discussion_r41580930  

Sure, that of course can be done easily, but does it really make sense  
given that the input is text from the source code? I mean it's not like the  
usual run-time case where the user enters a bad string and can enter a good  
one next time: if this fails it is because the string is wrong in the code,  
right?  
On 09/10/2015 8:08 AM, "Barend Gehrels" notifications@github.com wrote:  
  
> In include/boost/geometry/extensions/gis/projections/impl/pj_init.hpp  
> https://github.com/boostorg/geometry/pull/324#discussion_r41569150:  
>   
> > @@ -204,8 +205,14 @@ inline parameters pj_init(R const& arguments, bool use_defaults = true)  
> >   
> > ```  
> >  if (! s.empty())  
> >  {  
> > ```  
> > -        // TODO: IMPLEMENT SPLIT  
> > -        pin.to_meter = atof(s.c_str());  
> > -        std::size_t const divisor = s.find('/');  
> > -        if (divisor == std::string::npos)  
> > -            pin.to_meter = std::atof(s.c_str());  
> > -        else  
> > -        {  
> > -            std::string const numerator(s.substr(0, divisor)), denominator(s.substr(divisor + 1));  
> > -            pin.to_meter = std::atof(numerator.c_str()) / std::atof(denominator.c_str());  
>   
> Please check for denominator=0  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/geometry/pull/324/files#r41569150.

---

## Comment 10

> Username: barendgehrels  
> Created_at: 2015-10-09 06:46:06 UTC  
> Updated_at: 2015-10-11 12:36:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/324#discussion_r41601512  

It is a library, the string comes from somewhere. The user can enter the string as well.

---

## Comment 11

> Username: jeremy-murphy  
> Created_at: 2015-10-11 07:00:38 UTC  
> Updated_at: 2015-10-11 12:36:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/324#discussion_r41706451  

We're talking about the string that comes from the second column, `to_meter`, of `pj_units[]` in `pj_units.hpp`, right? I wouldn't think of that as a string the user enters though of course it is possible. If someone accidentally adds an entry with `100/0` then is run-time really the right place to catch it? Surely it can be caught with some compile-time check on the string literals?

---

## Comment 12

> Username: barendgehrels  
> Created_at: 2015-10-11 08:46:35 UTC  
> Updated_at: 2015-10-11 12:36:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/324#discussion_r41707541  

It can come from pj_units, but can also come from a line as  
`bg::projections::parameters par = bg::projections::detail::pj_init_plus(parameters);`  
where a user enters `to_meter=1/10`

---

## Comment 13

> Username: jeremy-murphy  
> Created_at: 2015-10-11 08:55:21 UTC  
> Updated_at: 2015-10-11 12:36:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/324#discussion_r41707593  

Ahh, OK, I missed that, thanks. (I also realized that compile-time checks in C++03 are trickier than I thought.) What should happen if either numerator or denominator are zero then? Throw an exception? I see a lot of magic exception numbers thrown throughout the code; are they documented?

---

## Comment 14

> Username: jeremy-murphy  
> Created_at: 2015-10-11 09:23:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/324#issuecomment-147175364  

OK it throws `proj_exception(-99)` for zeroes -- that -99 is just a placeholder until I understand what should be there.

---

## Comment 15

> Username: barendgehrels  
> Created_at: 2015-10-11 09:27:19 UTC  
> Updated_at: 2015-10-11 09:28:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/324#issuecomment-147175732  

You are right - the original code uses error-codes which are just converted to exceptions with those codes (also in the generation). To be decided. -99 is fine for now

---

## Comment 16

> Username: barendgehrels  
> Created_at: 2015-10-11 09:29:08 UTC  
> Updated_at: 2015-10-11 12:36:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/324#discussion_r41708010  

lexical_cast is indeed nice.  
but please add curly braces in every if...

---

## Comment 17

> Username: barendgehrels  
> Created_at: 2015-10-11 09:30:35 UTC  
> Updated_at: 2015-10-11 12:36:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/324#discussion_r41708022  

divisor is not changed to pos? Did you compile this code before commit?

---

## Comment 18

> Username: barendgehrels  
> Created_at: 2015-10-11 09:31:27 UTC  
> Updated_at: 2015-10-11 12:36:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/324#discussion_r41708025  

Sorry, did not mention this earlier, but in case of these kind of initializations we always declare on a separate line. So `double const denumerator =` etc

---

## Comment 19

> Username: barendgehrels  
> Created_at: 2015-10-11 09:32:17 UTC  
> Updated_at: 2015-10-11 12:36:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/324#discussion_r41708036  

.c_str() is probably redundant - at least you don't use it a few lines below

---

## Comment 20

> Username: barendgehrels  
> Created_at: 2015-10-11 09:32:42 UTC  
> Updated_at: 2015-10-11 12:36:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/324#discussion_r41708043  

Also here please add {}

---

## Comment 21

> Username: barendgehrels  
> Created_at: 2015-10-11 09:33:56 UTC  
> Updated_at: 2015-10-11 12:36:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/324#discussion_r41708049  

This comment can go now

---

## Comment 22

> Username: jeremy-murphy  
> Created_at: 2015-10-11 10:53:48 UTC  
> Updated_at: 2015-10-11 12:36:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/324#discussion_r41708740  

Sure, OK.

---

## Comment 23

> Username: jeremy-murphy  
> Created_at: 2015-10-11 10:54:08 UTC  
> Updated_at: 2015-10-11 12:36:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/324#discussion_r41708745  

Ah, yes, I was a bit rushed, thanks.

---

## Comment 24

> Username: jeremy-murphy  
> Created_at: 2015-10-11 10:56:53 UTC  
> Updated_at: 2015-10-11 12:36:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/324#discussion_r41708778  

OK, I'll try to remember but I'll probably forget a lot just because it is not my habit. Why do you prefer it that way?

---

## Comment 25

> Username: jeremy-murphy  
> Created_at: 2015-10-11 11:06:52 UTC  
> Updated_at: 2015-10-11 12:36:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/324#discussion_r41708862  

Ahhh, haha, I _thought_ I compiled it but it is the recurring I problem I have with working on Boost that my development copy is not my 'working' copy. I don't like bjam all that much so I never learnt to use the test suites but I probably should.

---

## Comment 26

> Username: jeremy-murphy  
> Created_at: 2015-10-15 23:19:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/324#issuecomment-148549634  

I think this is finished now -- was there anything left that you're unsure of or want changed?

---

## Comment 27

> Username: barendgehrels  
> Created_at: 2015-10-17 08:34:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/324#issuecomment-148895789  

Yes, it is finished, thanks a lot for the PR and processing all the comments. Will merge.

---
