# #114 - boost::algorithm::trim_copy() does not remove \b character [Closed]

> Username: testmigrator  
> Created at: 2023-04-09 07:34:08 UTC  
> Updated at: 2023-07-06 04:45:40 UTC  
> Closed at: 2023-07-06 04:45:40 UTC  
> Url: https://github.com/boostorg/algorithm/issues/114  

I was testing the functionality of boost::algorithm::trim_copy() API and noticed that when running boost::algorithm::trim_copy(std::string(" \t\r\n\b ")), the result did not remove the \b character. Is this a bug in the function's implementation? The documentation states that it should remove all leading and trailing whitespace characters, but it seems to be missing the backspace character. Can this issue be investigated and resolved? Thank you.  
  
BOOST_AUTO_TEST_CASE(testTrim) {  
    BOOST_CHECK_EQUAL(FOO, boost::algorithm::trim_copy(FOO + "  "));  
    BOOST_CHECK_EQUAL(FOO, boost::algorithm::trim_copy(" " + FOO + "  "));  
    BOOST_CHECK_EQUAL(FOO, boost::algorithm::trim_copy(" " + FOO));  
    BOOST_CHECK_EQUAL(FOO, boost::algorithm::trim_copy(FOO + ""));  
    // BOOST_CHECK_EQUAL("", boost::algorithm::trim_copy(std::string(" \t\r\n ")));  
    // \b  
    BOOST_CHECK_EQUAL("", boost::algorithm::trim_copy(std::string(" \t\r\n\b ")));  
}

---

## Comment 1

> Username: mclow  
> Created at: 2023-04-09 13:58:39 UTC  
> Url: https://github.com/boostorg/algorithm/issues/114#issuecomment-1501135403  

My initial thought is that backspace is not usually considered a "whitespace character".  
  
if you look at https://en.cppreference.com/w/cpp/string/byte/isspace, (you'll see that backspace (0x08) is considered a 'control character' not a 'whitespace character'.  
  
If you really want to strip backspaces, you can write a custom predicate that matches both whitespace and backspace, and pass that to `[boost](boost::algorithm::trim_copy)`
