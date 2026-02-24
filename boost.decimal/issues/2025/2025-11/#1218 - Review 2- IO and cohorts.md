# #1218 - Review 2: IO and cohorts [Closed]

> Username: jzmaddock  
> Created at: 2025-11-05 19:39:25 UTC  
> Updated at: 2026-01-29 15:22:52 UTC  
> Closed at: 2025-11-14 08:47:29 UTC  
> Url: https://github.com/boostorg/decimal/issues/1218  

I realize this is a contentious subject, and maybe you have made some progress already, but the important thing is to get clarity on what happens, and to provide the option if required (I have no great view on what should be the default).  
  
Here are the review comments:  
  
## `<charconv>` Support  
  
I think my general suggestion (but please read the explanation below) is to  
extend the format specifiers with a `normalized` flag that needs to be added  
(with or |) to the format spec to not get the cohorts behavior.  So that  
printing and reading in the cohorts would be the default behavior, allowing  
round-tripping of decimal FP values.  
  
### `from_chars`  
  
The handling of cohorts is not specified for this function.  Ideally the  
function would either create the appropriate cohort for the input string, such  
as a decimal with {200, -2} for "2.00", or, alternatively we need to extend the  
format with a `normalized` flag.  
  
### `to_chars`  
  
The `to_chars` documentation says that cohorts are not obeyed and the  
normalized value is printed .  I believe that that is a grave mistake.  The  
"cohorts" are an important part of the system and indicate the precision of the  
number.  I also suspect that normalized printing is more expensive than  
printing the cohort, so people pay for something that they don't necessarily  
want to use.  
  
I suggest that either cohorts should be obeyed or extend the format spec with  
an optional `normalized` flag.  
  
To chars could also use a description on how to calculate the necessary output  
buffer size so any number could be printed (including but not limited to the  
funny ones like fixed notation of very large or very small numbers, even  
denormals.)  We may only write the sizes here and add a separate page where the  
calculation is described.  Of course we would need different sizes for the  
different formats and document how precision changes those.  I can see that  
`limits::max_chars` is there but that covers only one format and without  
precision.  
  
## Formatting Support  
  
I do not see formatting support stating that if no precision is specified the  
internally-contained (cohort) precision will be used.  I believe that that is a  
mandatory feature for usability (also for `to_chars`).

---

## Comment 1

> Username: mborland  
> Created at: 2025-11-06 08:29:13 UTC  
> Url: https://github.com/boostorg/decimal/issues/1218#issuecomment-3495836419  

I have added an additional `chars_format` to preserve cohort for both `to_chars` and `from_chars`. This is then extended to `{fmt}` and `<format>` since both of those just use `<charconv>` in the background. There's nothing I can do for iostreams, but that doesn't seem to be of any real concern.
