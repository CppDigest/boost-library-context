# #1022 - Questions about known vulnerabilities [Open]

> Username: NinjaCross  
> Created at: 2025-03-24 08:14:24 UTC  
> Updated at: 2025-03-24 13:47:19 UTC  
> Url: https://github.com/boostorg/boost/issues/1022  

I love Boost very much, but recently I had problems embedding it into some projects, because many customers started raising concerns related to cybersecurity and dependencies management.  
  
In recent times, many industrial sectors have been (or will be in the very near future) impacted by regulations related to Quality Assurance and Cybersecurity (i.e. the EU [Cyber Resilience Act](https://digital-strategy.ec.europa.eu/en/policies/cyber-resilience-act)).  
This forces companies that produce and release software, to keep track of the used dependencies and their vulnerabilities.  
For **compiled** packages released through centralized repositories (i.e. NuGet, NPM, Maven, etc), keeping track of known vulnerabilities is easy, there are plenty archives that enumerates their [CVE](https://cve.mitre.org/)/[CWE](https://cwe.mitre.org/) codes by package version.  
Even GitHub itself contains the [GitHub Advisory Database](ttps://github.com/advisories)  
  
For a project like Boost, it's hard to keep track of every included library, and even harder to keep track of their individual potential/known vulnerabilities.  
I understand that there is a [Formal Review Process](https://www.boost.org/community/reviews.html) and [some testing](https://www.boost.org/development/testing.html) involved in the production of a Boost version, but as far as I can see, there is (almost) nothing related to cybersecurity and potential vulnerabilities.  
  
Am I wrong ? Is there some sort of well-known vulnerabilities list for every version of Boost ?  
  
Also, since Boost is released as source, and not as packaged & versioned binaries, it's non-trivial to use it in projects where the company is enforced to keep track of every dependency _identity_ (sources could be changed, thus diverging from the "official" release)  
  
Without such information, a company that use Boost could be forced to perform from scratch its own vulnerability assessment directly on the sources, and as you can imagine, this would be a huge effort.  
  
Any suggestion would be greately appreciated, thanks !

---

## Comment 1

> Username: mclow  
> Created at: 2025-03-24 13:47:18 UTC  
> Url: https://github.com/boostorg/boost/issues/1022#issuecomment-2748192587  

You will get a lot more visibility if you post this to the boost-developers mailing list.  See https://lists.boost.org/mailman/listinfo.cgi/boost for more info.
