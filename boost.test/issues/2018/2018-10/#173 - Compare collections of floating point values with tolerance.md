# #173 - Compare collections of floating point values with tolerance [Closed]

> Username: Flamefire  
> Created at: 2018-10-19 11:54:02 UTC  
> Updated at: 2022-03-07 20:05:02 UTC  
> Closed at: 2020-04-08 17:14:27 UTC  
> Url: https://github.com/boostorg/test/issues/173  

Currently you can use **either**:  
  
- `BOOST_TEST_REQUIRE( output == outputDesired, boost::test_tools::per_element() );`  
- `BOOST_TEST_REQUIRE( output == outputDesired, boost::test_tools::tolerance( 1e-12f ) );`  
  
But you cannot use both.  
  
What is the preferred way to compare collections (e.g. vectors) of floating point values?  
  
This is a common use case for testing numeric algorithms: Check results of optimized version vs. naive implementation.

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2018-11-11 23:38:03 UTC  
> Url: https://github.com/boostorg/test/issues/173#issuecomment-437716238  

What about using per-unit tolerance with a decorator?  
https://www.boost.org/doc/libs/1_68_0/libs/test/doc/html/boost_test/utf_reference/testing_tool_ref/decorator_tolerance.html

---

## Comment 2

> Username: Flamefire  
> Created at: 2018-11-12 07:26:55 UTC  
> Url: https://github.com/boostorg/test/issues/173#issuecomment-437782201  

This is our current work-around but limits the abilities. Imagine a test where you test matrix operations and first have to assert that some intermediate value (e.g. input, transposed matrix, ...) is exactly equal and then test the result of some calculation. Yes those can be put into separate unit tests, but maybe they are so closely related that one does not want to do this.

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2020-04-08 14:07:59 UTC  
> Url: https://github.com/boostorg/test/issues/173#issuecomment-610981396  

@Flamefire there is a WIP branch for this: `topic/PR-187-BOOST_TEST-with-tolerance-message`: it would be nice if you can give a try until I polish it up. It should address this particular issue together with #242 and #187.  
  
I'll try to make that for 1.73.

---

## Comment 4

> Username: raffienficiaud  
> Created at: 2020-04-08 17:14:27 UTC  
> Url: https://github.com/boostorg/test/issues/173#issuecomment-611082088  

Merged to master.

---

## Comment 5

> Username: Char-Aznable  
> Created at: 2021-11-28 06:24:32 UTC  
> Url: https://github.com/boostorg/test/issues/173#issuecomment-980847793  

@raffienficiaud  I struggle to find documentation on the OP's proposal. What exactly is the solution to this problem that get merged?

---

## Comment 6

> Username: raffienficiaud  
> Created at: 2022-03-07 20:05:02 UTC  
> Url: https://github.com/boostorg/test/issues/173#issuecomment-1061083335  

@Char-Aznable sorry for the late answer: now it is possible to combine both `boost::test_tools::per_element()` and `boost::test_tools::tolerance( 1e-12f )`. The documentation is only in the [change log](https://www.boost.org/doc/libs/1_78_0/libs/test/doc/html/boost_test/change_log.html#boost_test.change_log.boost_test_v3_13_boost_1_73) of 1.73.  
  
If we need more, let me know I'll open a ticket.
