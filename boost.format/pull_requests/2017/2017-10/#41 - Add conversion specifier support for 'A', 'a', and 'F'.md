# #41 Add conversion specifier support for 'A', 'a', and 'F' [Merged]

> Username: jeking3  
> Created at: 2017-10-24 03:10:00 UTC  
> Updated at: 2017-10-24 19:51:27 UTC  
> Merged at: 2017-10-24 16:26:18 UTC  
> Closed at: 2017-10-24 16:26:18 UTC  
> Url: https://github.com/boostorg/format/pull/41  

Using the format_matrix tool, I have ensured the only changes were for F, A, and a conversion specifiers.  I did a little grouping/prettying-up the conversion specifier switch statement in code as part of this PR.  
  
Note that ``F`` versus ``f`` really only matters when using the float constants ``INFINITY`` or ``NAN``, however ``F`` is supported by ISO C99 so we're supporting it here as well.  
  
With-Commit values are from msvc-14.1.  
  
| String | Data | Pre-Commit | With-Commit |  
| -: | :- | :- | :- |   
| ``%F`` | ``1234567.890123f`` | boost::bad_format_string: format-string is ill-formed | ``1234567.875000`` |  
| ``%A`` | ``1234567.890123f`` | boost::bad_format_string: format-string is ill-formed | ``0X1.2D687EP+20`` |  
| ``%a`` |   ``1234567.890123f`` | boost::bad_format_string: format-string is ill-formed | ``0x1.2d687ep+20`` |  
  
This closes #30

---
