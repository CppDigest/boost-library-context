# #250 - XML/JUNIT output not sanitized for invalid UTF-8 [Open]

> Username: bradenmcd  
> Created at: 2020-01-30 22:00:04 UTC  
> Updated at: 2020-03-30 16:02:22 UTC  
> Url: https://github.com/boostorg/test/issues/250  

When tests use (deliberately) invalid UTF-8 strings as data, those strings are written directly to the XML/JUNIT log output. When test data is included in the output, this results in output that cannot be parsed by an XML parser.

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2020-03-30 16:02:22 UTC  
> Url: https://github.com/boostorg/test/issues/250#issuecomment-606089314  

Do you have an example?
