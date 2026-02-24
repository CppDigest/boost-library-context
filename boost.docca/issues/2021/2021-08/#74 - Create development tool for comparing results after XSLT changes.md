# #74 - Create development tool for comparing results after XSLT changes [Closed]

> Username: evanlenz  
> Created at: 2021-08-29 23:41:07 UTC  
> Updated at: 2021-08-30 23:21:22 UTC  
> Closed at: 2021-08-30 23:21:22 UTC  
> Url: https://github.com/boostorg/docca/issues/74  

I have a pair of rudimentary shell scripts that I've copied into each docca-based doc project for comparing doc build results after XSLT changes are made (after normalizing the output to remove noise). I will put these into separate pull requests to get people's feedback on whether they are acceptable to include in each repository in their current form.  
  
The purpose of this is to guard against XSLT regressions in docca, thus enabling more fluid and confident updates to the code. It supplements/augments the minimal but growing test suite we have for specific issues in docca.

---

## Comment 1

> Username: evanlenz  
> Created at: 2021-08-30 00:52:30 UTC  
> Url: https://github.com/boostorg/docca/issues/74#issuecomment-907922044  

I came to my senses, bit the bullet, and reworked these scripts so there is only one copy of them (in a new docca/util directory). That way it won't muddy up the other projects and will be easier to maintain. So now just watch for one pull request on the docca project.
