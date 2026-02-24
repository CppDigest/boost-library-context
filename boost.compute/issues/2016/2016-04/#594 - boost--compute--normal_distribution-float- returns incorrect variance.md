# #594 - boost::compute::normal_distribution<float> returns incorrect variance [Closed]

> Username: MossanTokyo  
> Created at: 2016-04-24 13:35:52 UTC  
> Updated at: 2016-04-24 15:17:03 UTC  
> Closed at: 2016-04-24 14:59:48 UTC  
> Url: https://github.com/boostorg/compute/issues/594  

I tried to generate random number using boost compute random, but it seems it returns wrong variance.  
  
My script can be found at [here](https://gist.github.com/MossanTokyo/c202249079e3c63f0cc16dfed0599504#file-compute_bug-cpp).

---

## Comment 1

> Username: jszuppe  
> Created at: 2016-04-24 14:12:12 UTC  
> Url: https://github.com/boostorg/compute/issues/594#issuecomment-213964365  

Which version of Boost.Compute have you used? Because this bug was issued here https://github.com/boostorg/compute/issues/587 and should be fixed by pull-request https://github.com/boostorg/compute/pull/591 (see `develop` branch).

---

## Comment 2

> Username: MossanTokyo  
> Created at: 2016-04-24 14:59:48 UTC  
> Url: https://github.com/boostorg/compute/issues/594#issuecomment-213978741  

Ah, I see. I was on the main branch 44f08e3932e8ec1101e1aa137e404c3da5fddb5c.  
  
After I swtiched the develop branch, the issue was solved. Thank you for your information!

---

## Comment 3

> Username: jszuppe  
> Created at: 2016-04-24 15:01:40 UTC  
> Url: https://github.com/boostorg/compute/issues/594#issuecomment-213978879  

Glad I could help!

---

## Comment 4

> Username: jagerman  
> Created at: 2016-04-24 15:17:03 UTC  
> Url: https://github.com/boostorg/compute/issues/594#issuecomment-213982115  

Just a quick tangential comment about your script: if you're generating a lot of normals (**not** using compute), you'll find that boost::random::normal_distribution is considerably faster than std::normal_distribution; both libstdc++ and MSVC appear to be using a Box-Muller algorithm, while boost::random::n_d uses the much faster ziggurat algorithm (since 1.56.0).  
  
(Compute can't do the same, as far as I can see, because the ziggurat algorithm is a rejection sampling algorithm that requires branching and looping until an acceptable value is found).
