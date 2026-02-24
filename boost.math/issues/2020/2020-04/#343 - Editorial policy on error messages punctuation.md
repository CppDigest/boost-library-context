# #343 - Editorial policy on error messages punctuation [Closed]

> Username: pabristow  
> Created at: 2020-04-17 14:19:58 UTC  
> Updated at: 2024-01-24 21:42:00 UTC  
> Closed at: 2024-01-24 21:42:00 UTC  
> Url: https://github.com/boostorg/math/issues/343  

Editorial policy on error messages punctuation is a bit inconsistent, and can lead to some inconsistency and ugliness.  
  
Is there agreement that error messages should start with a Capital Letter (unless a C++ name) and end with punctuation, and a trailing space?  
  
The conventional trailing space after punctuation is to neatly separate any following output on the same line.   
  
Personally I'd like to end information messages with a period (full stop to native English speakers?)  
  
and messages alerting to some surprising behaviour like errors, should end with an exclamation mark!  
  
  Error in function float_prior<float>(float): Argument must be finite, but got infinity!   
  
But this would be a change in very, very many places, so  
I don't propose to go through everything changing things, but new messages might use this format, and any code being revised could have messages updated to conform.  
  
Views?

---

## Comment 1

> Username: NAThompson  
> Created at: 2020-04-17 15:04:18 UTC  
> Url: https://github.com/boostorg/math/issues/343#issuecomment-615295993  

> Views?  
  
Unfortunately this sort of consistency is really hard to achieve in practice; you need basically need to do code reviews with a checklist.  
  
It sounds like a lot of work to go back through and make everything consistent.  
  
I agree with your stylistic advice, but who will do the work?

---

## Comment 2

> Username: pabristow  
> Created at: 2020-04-17 17:04:30 UTC  
> Url: https://github.com/boostorg/math/issues/343#issuecomment-615359726  

I was thinking about a rolling program ;-)  Along with docs updates.  
  
Some automation may be possible.  
  
I doubt that complete consistency is possible, but the user experience can't be made worse?  
  
(Unless people are relying on the exact layout of error messages?)

---

## Comment 3

> Username: jzmaddock  
> Created at: 2020-04-17 17:20:41 UTC  
> Url: https://github.com/boostorg/math/issues/343#issuecomment-615367173  

I would disagree about the trailing space: if the user wants to format more than one message on one line they should do so in their code IMO.  I'm not as keen on exclamation marks as you are ( ha! :) ) but could go either way on that.  
  
In fact after a quick grep I found only one message that definitely did not adhere to those guidelines - in owens_t.hpp.  
  
Everything else goes through the policies::raise* functions which generate fairly consistent boilerplated messages (I hope).  
  
Did you spot something else I've missed?

---

## Comment 4

> Username: pabristow  
> Created at: 2020-04-18 10:42:13 UTC  
> Url: https://github.com/boostorg/math/issues/343#issuecomment-615839605  

I found some messages that were missing terminal full stops (periods to you Yanks) that triggers my musing about this.  
  
I'm not sure that all the messages end with a newline?  If they do, then a trailing space has no effect, but I think that logically it is part of formatting to write 'punctuation  space'.  
  
I really like proper punctuation, with plenty of commas and dashes to help reading, but  
  
I love exclamation marks !!!!!!!  
  
But I'm not holding religious views on this, just asking...  
  
Should I ask on the Boost lists?  (Since so many people or furloughed or confined to their quarters ;-)

---

## Comment 5

> Username: NAThompson  
> Created at: 2020-04-18 11:10:31 UTC  
> Url: https://github.com/boostorg/math/issues/343#issuecomment-615843066  

I'm not convinced that starting a flame war on the mailing lists is a good idea!  
  
Honestly, when programming we're so absorbed in getting the program to work that there's no mental space left to worry about formatting. Even if a miracle happened and everyone agreed to a clear and definitive style guide, we'd still forget about it when writing code!

---

## Comment 6

> Username: NAThompson  
> Created at: 2024-01-24 21:42:00 UTC  
> Url: https://github.com/boostorg/math/issues/343#issuecomment-1908962712  

Closing as no traction was achieved.
