# #204 - Feature Request: Allow specifying timeouts for test cases with datasests. [Closed]

> Username: dudamoos  
> Created at: 2019-02-08 23:30:25 UTC  
> Updated at: 2019-02-18 21:21:16 UTC  
> Closed at: 2019-02-18 21:20:52 UTC  
> Url: https://github.com/boostorg/test/issues/204  

Currently, the timeout decorator doesn't do anything for test suites. Since test cases with datasets are implemented with test suites under the hood, that means the timeout decorator doesn't actually take effect for them, which is surprising. I am currently using a workaround (shown below) that works in my tests, but most people (including myself) will hate it because it exploits a loophole in what would otherwise be (and certainly looks like) undefined behavior.  
  
```c++  
BOOST_TEST_DECORATOR(*boost::unit_test::fixture([]() {  
    struct suite_timeout_propagator : boost::unit_test::test_tree_visitor {  
        unsigned curTimeout;  
  
        virtual void visit(const boost::unit_test::test_case& tc) {  
            // This is technically *not* undefined behavior because object referred to  
            // by the reference isn't actually const.  
            const_cast<boost::unit_test::test_case&>(tc).p_timeout.set(curTimeout);  
        }  
  
        virtual bool test_suite_start(const boost::unit_test::test_suite& suite) {  
            curTimeout = suite.p_timeout;  
            return curTimeout > 0;  
        }  
    } propagator;  
  
    boost::unit_test::traverse_test_tree(  
        boost::unit_test::framework::current_test_unit(),  
        propagator);  
}))  
```

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2019-02-10 19:30:11 UTC  
> Url: https://github.com/boostorg/test/issues/204#issuecomment-462163671  

Thanks for the report. Your solution is a bit complicated :) the problems comes from the fact that the dataset test cases are within an (automatically created) test suite, and the test suite consumes all the decorators first. I am working on a solution.  
  
This is very related to #138

---

## Comment 2

> Username: raffienficiaud  
> Created at: 2019-02-13 16:36:22 UTC  
> Url: https://github.com/boostorg/test/issues/204#issuecomment-463270207  

I believe this is a duplicate of #133 and #138 as the decorators are not propagated properly from the parent test-suite. You can try the branch `topic/GH-138-decorator-dataset-testcase` and let me know if that works for you.

---

## Comment 3

> Username: dudamoos  
> Created at: 2019-02-13 18:29:30 UTC  
> Url: https://github.com/boostorg/test/issues/204#issuecomment-463312465  

I'm trying to test this, but I'm having trouble applying the patch on top of Boost 1.68.

---

## Comment 4

> Username: raffienficiaud  
> Created at: 2019-02-13 19:20:46 UTC  
> Url: https://github.com/boostorg/test/issues/204#issuecomment-463331247  

I believe this depends on whatever happened on develop/master since then. 2 easiest solutions might be:  
  
1. you clone boost, then cd libs/test, then checkout this branch, then go back to the root to generate b2 and then boost.test. You may also clone boost at the specific 1.68 tag and then checkout this branch for libs/test   
1. you have a tarball of 1.68, you deflate it somewhere, then go to libs/test and replace the folder content by the git clone of boost.test, then go back to the root an copy the content of libs/test/include/boost into the /include, then generate b2 and boost.test  
  
I am not sure that the second solution would work though ...

---

## Comment 5

> Username: dudamoos  
> Created at: 2019-02-13 21:20:19 UTC  
> Updated at: 2019-02-14 00:59:37 UTC  
> Url: https://github.com/boostorg/test/issues/204#issuecomment-463376317  

I did option 1 but rebased the branch on top of next-internal to take advantage of the other recent changes. The branch does appear to fix the issue.  
  
When is Boost 1.70 expected to be released? I'd like to get everyone using the updated/patched version, but the limiting factor is our automated cross-compile environment. I'd either need to just bump what version we use or have a patch against the existing tarball of 1.68 or 1.69. If the patch against 1.69 is simple (ideally only needing the diff of the branch merge commit), I might be able to just bump us to 1.69 and patch in the changes until the 1.70 release.

---

## Comment 6

> Username: raffienficiaud  
> Created at: 2019-02-16 02:28:55 UTC  
> Url: https://github.com/boostorg/test/issues/204#issuecomment-464275289  

According to [this](https://www.boost.org/development/index.html), the plan would be in first mid of April.  
  
I usually merge very few commits to master, so replaying the path 1.68->1.69->current-develop should be contained in less than 10 commits diffs. The other option would be to apply the diff `1.68..next-internal` on top of your copy of 1.68:  
  
     git diff  --minimal boost-1.68.0..next-internal  > fix-defects.patch
