# #352 - Master list of macro and symbol collision issue reports in all Boost libraries [Open]

> Username: ned14  
> Created at: 2019-12-16 14:49:41 UTC  
> Updated at: 2020-07-21 18:03:16 UTC  
> Url: https://github.com/boostorg/boost/issues/352  

Hopefully github will draw a line through each of these as/if they get closed so one can see an overview of progress. When/if they are all closed, build tooling ought to add a build-time check to prevent breakage of Boost library guidelines in the future.  
  
- ~https://github.com/boostorg/container/issues/136~  
- https://github.com/boostorg/date_time/issues/118  
- ~https://github.com/boostorg/exception/issues/28~  
- https://github.com/boostorg/fusion/issues/218  
- ~https://github.com/boostorg/gil/issues/410~  
- ~https://github.com/boostorg/graph/issues/193~  
- ~https://github.com/boostorg/iterator/issues/53~  
- ~https://github.com/boostorg/lambda/issues/18~  
- ~https://github.com/boostorg/math/issues/278~  
- ~https://github.com/boostorg/multi_array/issues/25~  
- ~https://github.com/boostorg/parameter/issues/94~  
- https://github.com/boostorg/phoenix/issues/90  
- ~https://github.com/boostorg/polygon/issues/33~  
- https://github.com/boostorg/program_options/issues/88  
- ~https://github.com/boostorg/property_map/issues/12~  
- https://github.com/boostorg/python/issues/281  
- ~https://github.com/boostorg/qvm/issues/22~  
- ~https://github.com/boostorg/serialization/issues/185~  
- https://github.com/boostorg/spirit/issues/562  
- ~https://github.com/boostorg/stacktrace/issues/85~  
- https://github.com/boostorg/thread/issues/302  
- https://github.com/boostorg/type_traits/issues/135  
- ~https://github.com/boostorg/wave/issues/52~

---

## Comment 1

> Username: apolukhin  
> Created at: 2019-12-16 19:28:07 UTC  
> Url: https://github.com/boostorg/boost/issues/352#issuecomment-566206584  

Can the boost.inspect tool be tought to detect non `BOOST_` prefixed maceo?

---

## Comment 2

> Username: apolukhin  
> Created at: 2019-12-16 19:37:34 UTC  
> Url: https://github.com/boostorg/boost/issues/352#issuecomment-566210357  

> Can the boost.inspect tool be tought to detect non `BOOST_` prefixed maceo?  
  
Oh, you've already done a PR https://github.com/boostorg/boost/pull/353

---

## Comment 3

> Username: hkaiser  
> Created at: 2019-12-18 10:55:02 UTC  
> Url: https://github.com/boostorg/boost/issues/352#issuecomment-566982260  

I would like to suggest to exclude macro symbols that are used as #include guards and are otherwise guaranteed to be unique (either contain a data/time or a guid) for this. Adding a `BOOST_` prefix to those does not add anything but additional effort on behalf of library maintainers.

---

## Comment 4

> Username: pdimov  
> Created at: 2019-12-18 15:01:44 UTC  
> Url: https://github.com/boostorg/boost/issues/352#issuecomment-567069067  

The motivation for consistently applying a BOOST_ prefix even to unique include guards is that companies want to prepare private Boost releases (that can coexist with the official one, or with another private one) by renaming BOOST_ to f.ex. MYBOOST_.

---

## Comment 5

> Username: hkaiser  
> Created at: 2019-12-18 15:22:50 UTC  
> Url: https://github.com/boostorg/boost/issues/352#issuecomment-567077871  

@pdimov Thanks Peter. Makes sense.
