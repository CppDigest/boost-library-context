# #62 Reimplement API for obtaining test error count [Closed]

> Username: Lastique  
> Created at: 2019-06-26 19:56:38 UTC  
> Updated at: 2020-05-08 10:50:52 UTC  
> Closed at: 2020-05-08 10:50:52 UTC  
> Url: https://github.com/boostorg/core/pull/62  

- This PR includes https://github.com/boostorg/core/pull/61.  
- `report_errors` now return an object of type `test_report`, which currently contains the number of failed tests. In the future, it may be extended with more additional data, if the need appears.  
- `test_report` can be converted to `int`, which is used to return a 0 or 1 from `main`. This preserves the canonical use case `return boost::report_errors();` ate the end of `main`.  
- `test_report` allows to obtain the actual number of failed tests via `error_count()`, so to test if the number of failed tests is as expected, use `return boost::report_errors().error_count() == expected ? 0 : 1;` construct.  
- `test_report` prohibits comparison against integers to catch the previously broken tests using the `return boost::report_errors() == expected;` pattern. Not only the comparison result was wrong, the returned value from `main` was the opposite from normal.  
- An additional function `get_error_count()` is provided to obtain the current number of errors at any point.  
  
Tests and docs updated accordingly.

---
