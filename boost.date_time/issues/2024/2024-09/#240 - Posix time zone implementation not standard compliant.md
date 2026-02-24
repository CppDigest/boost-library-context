# #240 - Posix time zone implementation not standard compliant [Open]

> Username: Bierkai  
> Created at: 2024-09-12 10:35:01 UTC  
> Updated at: 2025-10-10 12:04:30 UTC  
> Url: https://github.com/boostorg/date_time/issues/240  

[Boost documentation](https://www.boost.org/doc/libs/1_86_0/doc/html/date_time/local_time.html#posix_time_zone_intro) states that (emphasis mine)  
  
> A posix_time_zone is unique in that the object is created from a Posix time zone string (IEEE Std 1003.1). [...] "MST-7" [...] lies seven hours **west** of GMT  
  
But the [IEEE 1003.1 specification](https://pubs.opengroup.org/onlinepubs/9699919799/) states that (emphasis mine once again)  
  
> If preceded by a '-', the timezone shall be **east** of the Prime Meridian; otherwise, it shall be west (which may be indicated by an optional preceding '+' ).  
  
So the Boost documentation claims IEEE 1003.1 compliance, but uses an inverted interpretation of the offset symbol. The implementation (in Boost version 1.85) follows the Boost documentation, inverting the UTC offset for standard compliant specifications. The consequence is that UTC-to-timezone conversions (and vice versa) produce incorrect results for `posix_time_zone` objects constructed with standard-compliant specifications, as demonstrated by this code (based on Google test framework):  
  
```  
#include <boost/date_time/local_time/local_time.hpp>  
#include <boost/smart_ptr/make_shared.hpp>  
#include <gtest/gtest.h>  
  
TEST(PosixTimeZone, OffsetInversion) {  
  // Sample ptime: UTC noon  
  const boost::gregorian::date dt(2002, boost::gregorian::Jan, 10);  
  const boost::posix_time::ptime utc(dt, boost::posix_time::hours(12));  
  
  // The IEEE 1003.1 compliant specification for MST. Boost requires the incorrect "MST-7" to pass the tests (below)  
  auto tz = boost::make_shared<boost::local_time::posix_time_zone>("MST+7");  
  
  // Convert sample UTC ptime to MST  
  boost::local_time::local_date_time localized(utc, tz);  
  auto converted = localized.local_time();  
  
  // Mountain time (MST) is UTC-7, so wall clock time for UTC is 7 hours ahead of ("later than") MST, so...  
  const boost::posix_time::ptime expected(dt, boost::posix_time::hours(5)); // ...UTC noon should equal MST 5:00 a.m.  
  EXPECT_EQ(converted, expected) << "UTC ptime conversion to MST produced incorrect results";  
  
  // Boost applies the time zone's offset the wrong way around: hours are added instead of subtracted and vice versa  
  const boost::posix_time::ptime faulty(dt, boost::posix_time::hours(19)); // UTC noon should not equal MST 7:00 p.m.  
  EXPECT_NE(converted, faulty) << "UTC ptime conversion to MST applies inverted time zone offset";  
}  
```  
  
Expected behavior would be that (the test passes, i.e. that) calculations will be correct for `boost::local_time::posix_time_zone` instances constructed using a standard compliant IEEE 1003.1 time zone specification.

---

## Comment 1

> Username: stevenwdv  
> Created at: 2025-10-10 12:04:30 UTC  
> Url: https://github.com/boostorg/date_time/issues/240#issuecomment-3389730838  

Found #29
