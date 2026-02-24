# #924 - Boost Downloads [Open]

> Username: sdarwin  
> Created at: 2024-06-28 16:42:55 UTC  
> Updated at: 2024-06-28 16:42:55 UTC  
> Url: https://github.com/boostorg/boost/issues/924  

Hi,  
  
Copying a message posted in the Boost mailing list a few weeks ago:  
  
Summary:  
Boost downloads will be hosted at a new location, with the URL format https://archives.boost.io/release/1.85.0/source/boost_1_85_0.tar.gz  
  
Details:  
During the last couple of years the Boost downloads have been hosted on JFrog Artifactory, and before that on Bintray. JFrog has been very generous in providing the bandwidth and hosting services.  
  
Recently, bandwidth increased into the 150TB/month range. JFrog requested the download be moved behind a CDN, or migrated to another service entirely.  
  
At the C++ Alliance, we have set up load balanced AWS EC2 instances, and configured a Fastly CDN, on the domain archives.boost.io. Fastly offered a discounted bulk rate based on the large amount of traffic.  
  
The boost.org website now shows links to the new CDN. If you are using any automated scripts that point to jfrog.io, the existing  
JFrog links will continue to function until December 2024.
