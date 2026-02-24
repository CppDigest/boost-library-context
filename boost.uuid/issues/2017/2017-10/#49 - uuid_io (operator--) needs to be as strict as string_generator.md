# #49 - uuid_io (operator>>) needs to be as strict as string_generator [Closed]

> Username: jeking3  
> Created at: 2017-10-31 21:07:27 UTC  
> Updated at: 2018-07-05 07:48:02 UTC  
> Closed at: 2017-10-31 23:24:03 UTC  
> Url: https://github.com/boostorg/uuid/issues/49  

In https://github.com/boostorg/uuid/pull/17 changes were made to strengthen the string generator's parsing rules.  These rules were not applied, nor were tests added to the uuid_io implementation.  This was discovered through integration with codecov.io, which pointed out incomplete code coverage in uuid_io.

---

## Comment 1

> Username: jeking3  
> Created at: 2017-10-31 23:24:03 UTC  
> Url: https://github.com/boostorg/uuid/issues/49#issuecomment-340936138  

Resolving - stream processing of a uuid is more specific.  I will submit a PR to improve the unit test coverage.
