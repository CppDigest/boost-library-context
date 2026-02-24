# #222 - Error: boost::filesystem::create_directories: Function not implemented [Closed]

> Username: ShunLu91  
> Created at: 2021-12-10 10:20:56 UTC  
> Updated at: 2023-09-23 09:35:16 UTC  
> Closed at: 2021-12-10 14:38:22 UTC  
> Url: https://github.com/boostorg/filesystem/issues/222  

All the requirements were successfully installed and there is no error occurred.  
However, when I use denseflow to extract frames from videos, specifically the HMDB-51 datasets, such error appears:  
  
boost::filesystem::create_directories: Function not implemented: "../../data/hmdb51/rawframes/run/Two_Towers_1_run_f_cm_np2_fr_med_4"  
boost::filesystem::create_directories: Function not implemented: "../../data/hmdb51/rawframes/situp/Personal_Training_Workout_Tips_situp_f_nm_np1_le_goo_1"  
both 230 run/Two_Towers_1_run_f_cm_np2_fr_med_4.avi tvl1 done  
boost::filesystem::create_directories: Function not implemented: "../../data/hmdb51/rawframes/talk/Prelinger_FamilyLi1949_talk_h_nm_np1_fr_goo_15"  
boost::filesystem::create_directories: Function not implemented: "../../data/hmdb51/rawframes/hit/Schnaaper_Crew__Eisbaden_(2007)hit_f_cm_np1_le_bad_3"  
boost::filesystem::create_directories: Function not implemented: "../../data/hmdb51/rawframes/flic_flac/Acrobacias_de_un_fenomeno_flic_flac_f_cm_np1_fr_bad_1"  
both 442 situp/Personal_Training_Workout_Tips_situp_f_nm_np1_le_goo_1.avi tvl1 done  
boost::filesystem::create_directories: Function not implemented: "../../data/hmdb51/rawframes/somersault/Dive_and_roll_compilation(Parkour)somersault_f_cm_np1_fr_bad_0"  
boost::filesystem::create_directories: Function not implemented: "../../data/hmdb51/rawframes/swing_baseball/BaseballSwingAnalysis_swing_baseball_f_nm_np1_fr_med_8"  
boost::filesystem::create_directories: Function not implemented: "../../data/hmdb51/rawframes/wave/CharlieAndTheChocolateFactory_wave_u_nm_np1_fr_med_2"  
both 1502 somersault/Dive_and_roll_compilation(Parkour)somersault_f_cm_np1_fr_bad_0.avi tvl1 done  
both 18 swing_baseball/BaseballSwingAnalysis_swing_baseball_f_nm_np1_fr_med_8.avi tvl1 done  
boost::filesystem::create_directories: Function not implemented: "../../data/hmdb51/rawframes/run/THE_PROTECTOR_run_f_cm_np1_le_med_15"  
boost::filesystem::create_directories: Function not implemented: "../../data/hmdb51/rawframes/situp/6_Minute_Abs_Routine_situp_f_nm_np2_le_bad_1"  
boost::filesystem::create_directories: Function not implemented: "../../data/hmdb51/rawframes/talk/Prelinger_FamilyLi1949_talk_h_nm_np1_fr_goo_15"  
boost::filesystem::create_directories: Function not implemented: "../../data/hmdb51/rawframes/flic_flac/Acrobacias_de_un_fenomeno_flic_flac_f_cm_np1_fr_bad_1"  
boost::filesystem::create_directories: Function not implemented: "../../data/hmdb51/rawframes/hit/Schnaaper_Crew__Eisbaden(2007)hit_f_cm_np1_le_bad_3"  
boost::filesystem::create_directories: Function not implemented: "../../data/hmdb51/rawframes/swing_baseball/practicingmybaseballswing2009_swing_baseball_f_cm_np1_fr_med_19"  
boost::filesystem::create_directories: Function not implemented: "../../data/hmdb51/rawframes/somersault/Handstandk_nig_somersault_f_cm_np1_ba_med_5"  
boost::filesystem::create_directories: Function not implemented: "../../data/hmdb51/rawframes/wave/CharlieAndTheChocolateFactory_wave_u_nm_np1_fr_med_2"  
both 653 flic_flac/Acrobacias_de_un_fenomeno_flic_flac_f_cm_np1_fr_bad_1.avi tvl1 done  
both 867 hit/Schnaaper_Crew__Eisbaden(2007)_hit_f_cm_np1_le_bad_3.avi tvl1 done  
both 1078 talk/Prelinger_FamilyLi1949_talk_h_nm_np1_fr_goo_15.avi tvl1 done  
boost::filesystem::create_directories: Function not implemented: "../../data/hmdb51/rawframes/run/THE_PROTECTOR_run_f_cm_np1_le_med_15"  
boost::filesystem::create_directories: Function not implemented: "../../data/hmdb51/rawframes/situp/6_Minute_Abs_Routine_situp_f_nm_np2_le_bad_1"  
Raw frames (RGB and Flow) Generated  
  
Wait for help and great thanks to you.

---

## Comment 1

> Username: Lastique  
> Created at: 2021-12-10 14:38:22 UTC  
> Url: https://github.com/boostorg/filesystem/issues/222#issuecomment-991026483  

Duplicates https://github.com/boostorg/filesystem/issues/172.

---

## Comment 2

> Username: twilightmiao  
> Created at: 2023-09-23 08:42:23 UTC  
> Url: https://github.com/boostorg/filesystem/issues/222#issuecomment-1732256425  

Have you solved this problem? I also encountered a similar problem and the link above did not help me.

---

## Comment 3

> Username: ShunLu91  
> Created at: 2023-09-23 09:35:16 UTC  
> Url: https://github.com/boostorg/filesystem/issues/222#issuecomment-1732266986  

Sorry, I have not solved this issue yet though it has been two yeas. I remembered that I gave up using denseflow to process the dataset.
