# #110 - Steven's review, part 3 [Closed]

> Username: HDembinski  
> Created at: 2018-09-27 08:33:36 UTC  
> Updated at: 2019-02-05 08:22:06 UTC  
> Closed at: 2019-02-05 08:22:06 UTC  
> Url: https://github.com/boostorg/histogram/issues/110  

I vote to ACCEPT histogram into Boost.  
  
Critical issues:  
- Various exception safety and memory issues must  
 be fixed.  
- The reference documentation must be completed.  
 Specifically, various preconditions, requirements,  
 and assumptions must be made explicit.  
  
Tests (I'm running out of time, so this is a bit incomplete):  
  
- Some of run-fail tests would be better off as  
 just testing that the correct exception is thrown.  
 (i.e. BOOST_TEST_THROWS).  The ones that assert  
 must force debug builds (<variant>debug) as  
 assertions are a no-op in release builds.  
  
- For serialization tests, I think that you should  
 save a text archive permanently.  This will allow  
 you to detect cases where the serialization format  
 changes.  (Pass the archive on the command line.  
 The `run` rule in b2 has an `args` parameter.)  
  
utility.hpp:115: void deallocate(T*& p, std::size_t n) {  
 Don't take the argument by reference.  It's not guaranteed to work.  
  
axis_test.cpp:489: BOOST_TEST_EQ(db.size(), 5); // axis_type, char,  
double, long + ???  
 The ??? is an internal structure created by vector  
 to support checked iterators (specifically it's  
 std::_Container_proxy).  This test will fail in  
 release builds.  
  
index_mapper_test.cpp:  
 I feel that this test is a bit too specific.  The important  
 part is the relationship between linearize/indices_to_index  
 (detail/axes.hpp), index_cache, and index_mapper.  
 The exact mapping doesn't matter from the point of  
 view of program correctness, as long as they are all  
 consistent.

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-02-05 08:22:06 UTC  
> Url: https://github.com/boostorg/histogram/issues/110#issuecomment-460550250  

all expect documentation (todo) implemented in develop branch
