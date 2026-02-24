# #19 Restore stream format state after using month_formatter::format_month [Closed]

> Username: nbougalis  
> Created at: 2014-09-12 05:46:32 UTC  
> Updated at: 2014-09-15 17:55:11 UTC  
> Closed at: 2014-09-15 17:55:11 UTC  
> Url: https://github.com/boostorg/date_time/pull/19  

`date_time::month_formatter` alters the format state of an output stream that is passed in by changing the `width` and `fill` characters but never restores them. This can result in unexpected output when using this function.  
  
Save the old `width` and `fill` character and restore them, effectively putting the stream back to its original format state before returning.  
  
I've only tested this on gcc 4.8.1, but it uses only functionality available in the standard library.

---

## Comment 1

> Username: mclow  
> Created_at: 2014-09-12 07:43:17 UTC  
> Url: https://github.com/boostorg/date_time/pull/19#issuecomment-55371277  

This is done in several other places in date_time. Maybe we should make a list and fix them all.

---

## Comment 2

> Username: nbougalis  
> Created_at: 2014-09-12 17:24:16 UTC  
> Updated_at: 2014-09-12 17:24:26 UTC  
> Url: https://github.com/boostorg/date_time/pull/19#issuecomment-55434670  

@mclow I'd be happy to do that and update this PR accordingly.

---

## Comment 3

> Username: mclow  
> Created_at: 2014-09-12 20:30:46 UTC  
> Url: https://github.com/boostorg/date_time/pull/19#issuecomment-55456695  

I'm pretty sure that _somewhere_ in boost there is a RAII class that saves/restores iostream states.

---

## Comment 4

> Username: mclow  
> Created_at: 2014-09-15 15:09:24 UTC  
> Url: https://github.com/boostorg/date_time/pull/19#issuecomment-55604235  

@nbougalis: I just noticed that this pull request is set up to pull into 'master'. It should go into 'develop' instead.

---

## Comment 5

> Username: nbougalis  
> Created_at: 2014-09-15 17:55:11 UTC  
> Url: https://github.com/boostorg/date_time/pull/19#issuecomment-55630100  

@mclow good catch - I'll close & repull.

---
