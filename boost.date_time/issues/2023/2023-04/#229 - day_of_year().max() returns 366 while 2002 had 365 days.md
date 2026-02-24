# #229 - day_of_year().max() returns 366 while 2002 had 365 days [Open]

> Username: testmigrator  
> Created at: 2023-04-09 07:24:55 UTC  
> Updated at: 2023-04-09 07:24:55 UTC  
> Url: https://github.com/boostorg/date_time/issues/229  

The year 2002 had a total of 365 days, but when calling the day_of_year().max() function, it returns 366. Is this a bug? It seems like the function is returning the maximum possible day of the year for a leap year instead of correctly accounting for the number of days in 2002. Can this issue be looked into and resolved? Thank you.  
  
BOOST_AUTO_TEST_CASE(testPropertyGetMaxMinValuesDayOfYear)  
{  
    ptime test(date(2004, 6, 9), time_duration(0, 0, 0));  
  
    BOOST_CHECK_EQUAL(1, test.date().day_of_year().min());  
    BOOST_CHECK_EQUAL(366, test.date().day_of_year().max());  
  
    test = ptime(date(2002, 6, 9), time_duration(0, 0, 0));  
  
    BOOST_CHECK_EQUAL(1, test.date().day_of_year().min());  
    BOOST_CHECK_EQUAL(365, test.date().day_of_year().max());  
}
