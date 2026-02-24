# #133 - documentation to filter-usage [Closed]

> Username: tobias-loew  
> Created at: 2020-10-30 13:27:25 UTC  
> Updated at: 2020-10-30 21:07:02 UTC  
> Closed at: 2020-10-30 21:07:02 UTC  
> Url: https://github.com/boostorg/log/issues/133  

Hi,  
  
the documentation in detailed/utilities.html says to use  
  
Filter="%Severity% > 3"  
  
which is wrong: one must not use numbers for the severity-level but one of the names denoting them from the enum boost::log::trivial::severity_level

---

## Comment 1

> Username: Lastique  
> Created at: 2020-10-30 21:07:02 UTC  
> Url: https://github.com/boostorg/log/issues/133#issuecomment-719799846  

No, the filter argument must correspond to the attribute value type that is actually used in log records. You can use any type as the severity level, including integers and enums, including ones defined by user. The example in the documentation is for an integer, which, BTW, is the default severity level type in `basic_severity_logger`.
