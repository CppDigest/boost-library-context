# #481 - Update Travis-CI to use new container infrastructure [Closed]

> Username: kylelutz  
> Created at: 2015-07-18 18:01:32 UTC  
> Updated at: 2016-03-02 21:59:40 UTC  
> Closed at: 2016-03-02 21:59:36 UTC  
> Url: https://github.com/boostorg/compute/issues/481  

We should update Boost.Compute to use the new container based Travis-CI builders. See: http://docs.travis-ci.com/user/migrating-from-legacy/.

---

## Comment 1

> Username: roshanrags  
> Created at: 2015-08-08 14:18:41 UTC  
> Url: https://github.com/boostorg/compute/issues/481#issuecomment-128986247  

A PR(https://github.com/travis-ci/apt-package-whitelist/pull/750) has been opened a few hours ago to add fglrx to the package whitelist. Also, the whitelist contains boost 1.55 and not 1.48. Is that OK?

---

## Comment 2

> Username: kylelutz  
> Created at: 2015-08-11 05:06:34 UTC  
> Url: https://github.com/boostorg/compute/issues/481#issuecomment-129707388  

That's good to hear! Once that goes in I think we should be able to rewrite the `travis.yml` file to use the new infrastructure. And testing against Boost 1.55 is fine, 1.48 is getting pretty old now (though it's probably still good to remain compatible with it).

---

## Comment 3

> Username: jszuppe  
> Created at: 2016-03-02 21:57:23 UTC  
> Url: https://github.com/boostorg/compute/issues/481#issuecomment-191451429  

I think this issue can be closed.
