# #390 Add collector accumulator [Merged]

> Username: wiso  
> Created at: 2023-11-06 11:52:04 UTC  
> Updated at: 2024-04-25 07:05:40 UTC  
> Merged at: 2024-04-25 07:05:40 UTC  
> Closed at: 2024-04-25 07:05:40 UTC  
> Url: https://github.com/boostorg/histogram/pull/390  

This is a simple implementation of collector accumulator, a very simple accumulator which stores all the values passed, as suggested in #349. Some new tests are implemented. Documentation is updated.  
  
I would like to have a code review. For sure these items are missing.  
  
- [x] add copyright  
- [x] check compilation with b2  
  
Probably implementing serialization for this is not a good idea.  
  
I had to change the continuous integration:  
   * fix how libraries are searched by `depinst.py`, since by default the `example` directory is not used  
   * move to Ubuntu 20.04 since 18.04 is no longer supported https://github.com/actions/runner-images/issues/6002. Because of that also moved from gcc5 to gcc7.

---

## Comment 1

> Username: wiso  
> Created_at: 2024-03-20 15:42:31 UTC  
> Url: https://github.com/boostorg/histogram/pull/390#issuecomment-2009881329  

is this project abandoned? @HDembinski

---

## Comment 2

> Username: HDembinski  
> Created_at: 2024-04-02 08:41:30 UTC  
> Url: https://github.com/boostorg/histogram/pull/390#issuecomment-2031412791  

Not at all, but I have a lot of things on my plate right now. I should be able to work on all the outstanding issues/PRs in the coming months.

---

## Review 3 [Changes requested]

> Username: HDembinski  
> Created_at: 2024-04-23 11:01:52 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/histogram/pull/390#pullrequestreview-2016876093  

Thanks for the effort, this looks very good. The implementation looks correct and the tests thorough.  
  
Before merging, I would like to explore whether this code can be generalized in such a way that one can pass several sample values to the collector. Thanks to the template system, this should work with this implementation already if the size of the value array is known at compile file. In other words, if one uses a `std::array` as ValueType. A test for this case should be added.  
  
However, I also see the need for a dynamic collector, where the size of the sample array is only known at runtime. While this can be handled with a `ValueType = std::vector<double>`, it is very inefficient since it will cause a lot of dynamic memory allocations.  
  
Perhaps it is necessary to write a separate class, called dynamic_collector, to implement this. Then this PR can be merged right now. But if the two implementations can be merged and use the same template class name, that would be nice.  
  
Implementing a dynamic collector efficiently could be achieved with a new container type which stores tuples of numbers. The size of the tuple is defined at construction time. Internally, it would be a `std::vector`, the tuples are stored consecutively in memory.

📁 .github/workflows/cov.yml

```diff
  32 |         git submodule update --init --depth 5 tools/build tools/boostdep
  33 |-         python tools/boostdep/depinst/depinst.py --git_args "--depth 5 --jobs 3" histogram
  33 |+         python tools/boostdep/depinst/depinst.py --git_args "--depth 5 --jobs 3" -I examples histogram
```

> Username: HDembinski  
> Created_at: 2024-04-23 10:38:16 UTC  
> Updated_at: 2024-04-23 11:01:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/390#discussion_r1576035176  

Please revert. We don't run coverage on examples, because all code should be covered by unit tests.


📁 .github/workflows/slow.yml

```diff
  35 |         git submodule update --init --depth 5 tools/build tools/boostdep
  36 |-         python tools/boostdep/depinst/depinst.py --git_args "--depth 5 --jobs 3" histogram
  36 |+         python tools/boostdep/depinst/depinst.py --git_args "--depth 5 --jobs 3" -I examples histogram
```

> Username: HDembinski  
> Created_at: 2024-04-23 10:38:28 UTC  
> Updated_at: 2024-04-23 11:01:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/390#discussion_r1576035447  

Revert.

---

📁 .github/workflows/slow.yml

```diff
  62 |         git submodule update --init --depth 5 tools/build tools/boostdep
  63 |-         python tools/boostdep/depinst/depinst.py --git_args "--depth 5 --jobs 3" histogram
  63 |+         python tools/boostdep/depinst/depinst.py --git_args "--depth 5 --jobs 3" -I examples histogram
```

> Username: HDembinski  
> Created_at: 2024-04-23 10:38:44 UTC  
> Updated_at: 2024-04-23 11:01:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/390#discussion_r1576035720  

Revert.

---

📁 .github/workflows/slow.yml

```diff
  82 |         git submodule update --init --depth 5 tools/build tools/boostdep
  83 |-         python tools/boostdep/depinst/depinst.py --git_args "--depth 5 --jobs 3" histogram
  83 |+         python tools/boostdep/depinst/depinst.py --git_args "--depth 5 --jobs 3" -I examples histogram
```

> Username: HDembinski  
> Created_at: 2024-04-23 10:38:54 UTC  
> Updated_at: 2024-04-23 11:01:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/390#discussion_r1576035978  

Revert.

---

📁 .github/workflows/slow.yml

```diff
 102 |         git submodule update --init --depth 5 tools/build tools/boostdep
 103 |-         python tools/boostdep/depinst/depinst.py --git_args "--depth 5 --jobs 3" histogram
 103 |+         python tools/boostdep/depinst/depinst.py --git_args "--depth 5 --jobs 3" -I examples histogram
```

> Username: HDembinski  
> Created_at: 2024-04-23 10:39:00 UTC  
> Updated_at: 2024-04-23 11:01:53 UTC  
> Url: https://github.com/boostorg/histogram/pull/390#discussion_r1576036080  

Revert.


📁 doc/guide.qbk

```diff
 450 | * [classref boost::histogram::accumulators::fraction fraction] accepts a boolean sample that represents success or failure of a binomial trial. It computes the fraction of successes. One can access the number of successes and failures, the fraction, the estimated variance of the fraction, and a confidence interval. The standard confidence interval is the Wilson score interval, but more interval computers are implemented in
 451 | `boost/histogram/utility`. Beware: one cannot pass `std::vector<bool>` to [classref boost::histogram::histogram histogram::fill], because it is not a contiguous sequence of boolean values, but any other container of booleans works and any sequence of values convertible to bool.
 452 |+ * [classref boost::histogram::accumulators::collector collector] accepts a sample and stores all values in a container. This is a very inefficient accumulator, since it stores all the data. It is useful to compute custom statistical estimations, in particular the ones that cannot be written as accumulators.
```

> Username: HDembinski  
> Created_at: 2024-04-23 10:48:55 UTC  
> Updated_at: 2024-04-23 11:01:53 UTC  
> Url: https://github.com/boostorg/histogram/pull/390#discussion_r1576047251  

```diff  
-* [classref boost::histogram::accumulators::collector collector] accepts a sample and stores all values in a container. This is a very inefficient accumulator, since it stores all the data. It is useful to compute custom statistical estimations, in particular the ones that cannot be written as accumulators.  
+* [classref boost::histogram::accumulators::collector collector] consists of a collection of containers, one per bin. It accepts samples and sorts the sample value into the corresponding container. The memory consumption of this accumulator is unbounded, since it stores each input value. It is useful to compute custom estimators, in particular, those which require access to the full sample, like a kernel density estimate, or which do not have online update algorithms (for example, the median).  
```


---

## Comment 4

> Username: HDembinski  
> Created_at: 2024-04-23 12:17:08 UTC  
> Url: https://github.com/boostorg/histogram/pull/390#issuecomment-2072149031  

Please merge current develop branch to fix the CI issues.

---

## Comment 5

> Username: HDembinski  
> Created_at: 2024-04-23 12:30:00 UTC  
> Url: https://github.com/boostorg/histogram/pull/390#issuecomment-2072175005  

You don't need to write a changelog message, that's why I removed that item from your todo list. Please add the Boost copyright notice to all new files.

---

## Comment 6

> Username: wiso  
> Created_at: 2024-04-23 12:34:09 UTC  
> Url: https://github.com/boostorg/histogram/pull/390#issuecomment-2072183423  

what should I write? I see a common  
  
```  
//  
// Distributed under the Boost Software License, version 1.0.  
// (See accompanying file LICENSE_1_0.txt  
// or copy at http://www.boost.org/LICENSE_1_0.txt)  
```  
  
What should be the first line?

---

## Comment 7

> Username: HDembinski  
> Created_at: 2024-04-23 14:15:12 UTC  
> Url: https://github.com/boostorg/histogram/pull/390#issuecomment-2072432768  

It should be your name and my name. I am working on a large patch for collector, please don't make any other changes to the collector at the moment.

---

## Comment 8

> Username: HDembinski  
> Created_at: 2024-04-24 08:37:07 UTC  
> Url: https://github.com/boostorg/histogram/pull/390#issuecomment-2074402514  

@henryiii The collector accumulator is finally moving forward and the implementation seems to be nearly ready. I would like to quickly see it in the boost-histogram Python package, too, since I want to use it in a Python study. What is your time budget right now, do you think you can look into this? Otherwise I can give it a try.

---

## Comment 9

> Username: HDembinski  
> Created_at: 2024-04-24 09:49:26 UTC  
> Url: https://github.com/boostorg/histogram/pull/390#issuecomment-2074552846  

@wiso I think the PR is almost ready to be merged. I added the copyright notices.  
  
Design changes and additions: I changed the template arguments for the collector, because your original interface was not DRY. I added missing serialization support. You were exposing the internal container, now the internal container remains hidden. Instead, I added basic methods to the collector, which make it iterable and allow element access, and give access to the memory buffer (this is needed by the Python boost-histogram wrapper).  
  
Tests are in place for the interface. I have added a test which checks that the collector template can work with a dynamic array container type as outlined above.   
  
Coverage is not 100% right now, but that will be fixed in future PRs. The tracking of line coverage in coveralls is a bit broken and I don't know yet how to fix this. I cannot bring coverage to 100 % if the tracking does not work correctly.  
  
Do you wish to add anything at this point?

---

## Comment 10

> Username: wiso  
> Created_at: 2024-04-24 14:51:33 UTC  
> Url: https://github.com/boostorg/histogram/pull/390#issuecomment-2075138228  

Hello @HDembinski , thank you very much for your improvement. I am not planning to add anything. I would be happy to see this included.  
About the Python package, I have not fully understood how it has been implemented, I thought it was a binding of boost-histogram, but I see several parts are rewritten. I don't have expertise on it, even if it is the main way I use hist.

---
