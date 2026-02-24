# #453 - Unable to disable PairSoft Pay Configuration: status remains enabled after closing and reopening [Closed]

> Username: nshudra  
> Created at: 2025-08-19 11:00:04 UTC  
> Updated at: 2025-08-19 11:01:35 UTC  
> Closed at: 2025-08-19 11:01:35 UTC  
> Url: https://github.com/boostorg/test/issues/453  

**Bug Description:**  
When attempting to disable the PairSoft Pay Configuration by toggling the Enable switch off and clicking the X icon to close the configuration screen, the configuration still shows as enabled upon reopening. This indicates that the disabled status is not being saved or reflected correctly.  
  
**Steps to Reproduce:**  
1. Navigate to the Integrated App Instances configuration page.  
2. Locate the PairSoft Pay Configuration section.  
3. Toggle the Enable switch to disable.  
4. Click the X icon to close the configuration screen.  
5. Reopen the configuration screen for the same instance.  
  
**Expected Result:**  
The PairSoft Pay Configuration should remain disabled when the page is reopened.  
  
**Actual Result:**  
The PairSoft Pay Configuration still shows as enabled after reopening.  
  
**Impact:**  
Users are unable to persistently disable PairSoft Pay Configuration, which may affect payment processing and system integrity.  
  
**Screenshot:**  
<img alt="image1" width="1904" src="https://github.com/user-attachments/assets/fcbd18ee-1124-4dad-ade0-b0b154d7fd58" />  
  
**Environment:**  
- Application: PaperSave  
- Module: Integrated App Instances  
- Instance: NetSuite Wolfe Electronics Staging  
- Browser: Chrome (Incognito)  
- Date/Time: 2025-08-19 16:24  
  
**Additional Notes:**  
- Please investigate if the configuration status is being saved correctly on disable action.
