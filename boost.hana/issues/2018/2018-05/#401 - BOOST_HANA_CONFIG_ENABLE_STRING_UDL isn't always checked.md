# #401 - BOOST_HANA_CONFIG_ENABLE_STRING_UDL isn't always checked [Closed]

> Username: xiangfan-ms  
> Created at: 2018-05-29 21:15:24 UTC  
> Updated at: 2018-06-04 18:35:15 UTC  
> Closed at: 2018-06-04 18:35:15 UTC  
> Url: https://github.com/boostorg/hana/issues/401  

The user defined literal '_s' is only available when the macro BOOST_HANA_CONFIG_ENABLE_STRING_UDL is defined. It depends on a GCC extension.  
  
Three tests under hana\example\map use it without checking the macro. You can find them by searching for macro 'BOOST_HANA_WORKAROUND_MSVC_NO_STRING_UDL' in the repo https://github.com/xiangfan-ms/hana.

---

## Comment 1

> Username: ldionne  
> Created at: 2018-05-30 03:45:28 UTC  
> Url: https://github.com/boostorg/hana/issues/401#issuecomment-393021090  

Thanks for the report. Will be fixed.
