# #420 - Configure Codecov for code coverage reports [Closed]

> Username: mloskot  
> Created at: 2020-01-15 12:13:26 UTC  
> Updated at: 2021-01-20 21:56:20 UTC  
> Closed at: 2021-01-20 21:56:20 UTC  
> Url: https://github.com/boostorg/gil/issues/420  

### Codecov  
  
The idea is to set up code coverage for tests run during the CI builds and collection of reports by https://codecov.io/  
  
There is number of Boost libraries that use Codecov which may serve as examples to set it up for GIL, for example Boost.Beast:  
  
- https://github.com/boostorg/beast/blob/46ac848fa46ce5b81f2bb5b221774d15c0cdd128/.travis.yml  
- https://github.com/boostorg/beast/blob/46ac848fa46ce5b81f2bb5b221774d15c0cdd128/azure-pipelines.yml  
  
### Coveralls Alternative  
  
Alternatives are Coveralls, but it does not seem to support merging reports as the Codecov does https://docs.codecov.io/docs/merging-reports. For example, Boost.Histogram uses Coveralls https://github.com/boostorg/histogram/blob/a872c6e1c32e3c6000317b07aec7e416c8939c75/.travis.yml  
  
### Volunteers?  
  
If you are interested in helping us to set the code coverage for GIL CI builds, awesome!  
Please, respond to this issue (or post to https://lists.boost.org/boost-gil).

---

## Comment 1

> Username: mloskot  
> Created at: 2021-01-20 21:56:20 UTC  
> Url: https://github.com/boostorg/gil/issues/420#issuecomment-763975112  

Closed by #532
