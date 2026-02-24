# #958 - NIST CPEs missing for boost versions above 1.69.0 [Open]

> Username: rnavarro2  
> Created at: 2024-09-30 21:10:50 UTC  
> Updated at: 2024-09-30 21:10:50 UTC  
> Url: https://github.com/boostorg/boost/issues/958  

We have a product that uses the boost:1.76.0 Third-Party package.  We would like to use its NIST CPE (Common Platform Enumeration), however, the CPEs are missing from the NIST CPE Dictionary for all versions newer than boost:1.69.0.   
  
The following are a list of errors in the CPE Dictionary that should also be corrected for boost.  
  
- All boost download URLS have changed  
  - from: [http://downloads.sourceforge.net/project/boost/boost/](http://downloads.sourceforge.net/project/boost/boost/)  
  - to: [https://sourceforge.net/projects/boost/files/boost/](https://sourceforge.net/projects/boost/files/boost/)  
  
- Download URL for boost:1.76.0 is:  
  - https://sourceforge.net/projects/boost/files/boost/1.76.0/  
  
- Reference: https://services.nvd.nist.gov/rest/json/cpes/2.0?keywordSearch=boost&keywordExactMatch  
  - CPEs for Boost are missing after 1.69.0  
  
I have sent an email to cpe_dictionary@nist.gov to notify them of the CPE Dictionary errors for boost versions above 1.69.0.  However, the email should probably come from the repository owners to get this fixed.
