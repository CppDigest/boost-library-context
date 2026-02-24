# #184 - Starting a second process destroys files in the boost_interprocess folder [Open]

> Username: lieutenantSchuh  
> Created at: 2022-08-17 07:38:04 UTC  
> Updated at: 2022-08-17 07:39:52 UTC  
> Url: https://github.com/boostorg/interprocess/issues/184  

If a second process is started that also uses boost interprocess, the existing files in the boost_interprocess folder are renamed and destroyed.  
  
Examples of new file names are:  
C06C55F6FFB1D8018806033B09B2D801B0530000F7FFFFFF  
C06C55F6FFB1D8018806033B09B2D801B0530000F8FFFFFF
