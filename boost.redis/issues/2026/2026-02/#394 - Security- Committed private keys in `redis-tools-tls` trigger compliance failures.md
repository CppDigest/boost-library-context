# #394 - Security: Committed private keys in `redis/tools/tls` trigger compliance failures [Closed]

> Username: segor  
> Created at: 2026-02-06 12:58:12 UTC  
> Updated at: 2026-02-18 10:15:46 UTC  
> Closed at: 2026-02-18 10:15:46 UTC  
> Url: https://github.com/boostorg/redis/issues/394  

### Problem  
During a security audit of our dependencies, we identified committed private keys in the repository:  
  
- redis/tools/tls/ca.key  
- redis/tools/tls/server.key  
  
  
While we understand these are likely used for testing and tooling, committing secrets to version control triggers high-severity alerts in enterprise compliance scanners (e.g., TruffleHog, GitHub Secret Scanning) and violates modern security protocols.  
  
### Supporting Standards  
* **OWASP:** [Secrets Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Secrets_Management_Cheat_Sheet.html) explicitly states that secrets should never be stored in version control.  
* **NIST SP 800-57:** Outlines strict requirements for the protection of private keys to maintain the integrity of a system's security posture.  
  
### Suggested Resolution  
We recommend removing these static files from the repository and instead generating them dynamically during the test or build bootstrap phase.   
This would resolve compliance flags for all downstream users and align the library with "Secure by Design" industry standards.  
  
Thank you for your work on this library!

---

## Comment 1

> Username: anarthal  
> Created at: 2026-02-11 19:56:19 UTC  
> Url: https://github.com/boostorg/redis/issues/394#issuecomment-3886739188  

Sorry for the late reply. These (and the ones in MySQL) are for CI use only, so this does not have any security implications beyond scanner flagging. Is there any way to mark these as "for test use only"? Will the scanner flag test keys embedded in source files?

---

## Comment 2

> Username: segor  
> Created at: 2026-02-12 09:30:47 UTC  
> Updated at: 2026-02-12 11:46:30 UTC  
> Url: https://github.com/boostorg/redis/issues/394#issuecomment-3889752147  

> These (and the ones in MySQL) are for CI use only  
  
I understand, but there is a tiny chance that someone will  deploy the private key on prod.  
  
> Is there any way to mark these as "for test use only"?  
  
no, as I know there is no such option. The only correct way for us is maintain a fork with secrets deleted.  
  
> Will the scanner flag test keys  
  
most scanners can detect secrets embedded  in source code files

---

## Comment 3

> Username: anarthal  
> Created at: 2026-02-12 14:17:25 UTC  
> Url: https://github.com/boostorg/redis/issues/394#issuecomment-3891218242  

I see. I will try to get this done before the next release. Thanks for the report.

---

## Comment 4

> Username: anarthal  
> Created at: 2026-02-12 14:22:17 UTC  
> Url: https://github.com/boostorg/redis/issues/394#issuecomment-3891242890  

Do certificates trigger this kind of errors, too?
