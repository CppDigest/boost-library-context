# #36 Python Port: Add get_target_variable to engine interface. [Closed]

> Username: frenchtoast747  
> Created at: 2014-09-09 21:12:12 UTC  
> Updated at: 2021-10-02 22:35:46 UTC  
> Closed at: 2014-09-10 13:55:14 UTC  
> Url: https://github.com/boostorg/build/pull/36  

Creates a sibling to `Manager.engine().set_target_variable()`: `get_target_variable()`.  
  
I noticed that `set_target_variable()` makes a call to `do_set_target_variable()` for overriding purposes. Should `get_target_variable()` have the same thing?

---

## Comment 1

> Username: vprus  
> Created_at: 2014-09-10 13:55:14 UTC  
> Url: https://github.com/boostorg/build/pull/36#issuecomment-55118032  

Aaron,  
  
I've merged your change, thanks. I've only merged the new function itself, skipping formatting change - I would prefer that formatting cleanup be separate patches, unless it's gonna be an inconvenience for you? Not sure we need do_set_target_variable - it might be a bit of overdesign, given that nobody overrides it.

---

## Comment 2

> Username: frenchtoast747  
> Created_at: 2014-09-10 14:04:48 UTC  
> Url: https://github.com/boostorg/build/pull/36#issuecomment-55119416  

My apologies.  My editor automatically trims whitespace on save and I must have forgotten to only commit the function change. Thanks for the merge.  
  
<div>-------- Original message --------</div><div>From: Vladimir Prus notifications@github.com </div><div>Date:09/10/2014  8:55 AM  (GMT-06:00) </div><div>To: boostorg/build build@noreply.github.com </div><div>Cc: Aaron Boman aaron@aaronboman.com </div><div>Subject: Re: [build] Python Port: Add get_target_variable to engine interface.  
  (#36) </div><div>  
</div>Aaron,  
  
I've merged your change, thanks. I've only merged the new function itself, skipping formatting change - I would prefer that formatting cleanup be separate patches, unless it's gonna be an inconvenience for you? Not sure we need do_set_target_variable - it might be a bit of overdesign, given that nobody overrides it.  
  
—  
Reply to this email directly or view it on GitHub.

---
