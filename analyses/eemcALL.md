Pion and Dijet A<sub>LL</sub> at Intermediate Rapidities With EEMC
===================================================================

Resources
----------

- [EEMC NIM Paper](https://www.star.bnl.gov/public/tpc/NimPapers/endcap/eemc_nim.pdf) 
- [2006 PRD Paper Web Page](https://drupal.star.bnl.gov/STAR/blog/drach09/2013/feb/28/2006-eemc-neutral-pions-paper-home-page)
- [2006 Paper Link](https://journals.aps.org/prd/abstract/10.1103/PhysRevD.89.012001)
- [All Past EEMC Analyses Page](https://drupal.star.bnl.gov/STAR/book/export/html/10124)
- [Similar Analysis Using Run 12](https://drupal.star.bnl.gov/STAR/system/files/run12EndcapDijetPreliminary.pdf)
- [Run 13 Analysis](https://drupal.star.bnl.gov/STAR/system/files/Amilkar_Poster_RHIC%2526AGS2019_v2.pdf)

**Dataset**
- RHIC Run 15
- `get_filelist` commands:
   get_file_list.pl -keys 'path,filename' -cond 'storage!=hpss,filetype=daq_reco_muDst,filename~st_physics,production=P16id,trgsetupname=production_pp200long_2015' -limit 0 -      distinct -delim '/'
   get_file_list.pl -keys 'path,filename' -cond 'storage!=hpss,filetype=daq_reco_muDst,filename~st_physics,production=P16id,trgsetupname=production_pp200long2_2015' -limit 0 -      distinct -delim '/'


**Analysis Tools**

- UCR Spin Analysis Tools Github repository: [BrightStar](https://github.com/latifkabir/BrightSTAR)
- Calss references for EEMC pion Tree: [here](https://www.star.bnl.gov/webdata/dox/html/dir_f55c6bc819b98feb2a7a93228ad627a8.html)

**Excercises**

- Document `get_filelist` command
- Check that we have enough longitudinal dataset to do the analysis: total number of runs, number of events, number of fills, approximate total file sizes
- Generate DST using scheduler
- Make basic QA plots: 
   - Pion invariant mass
   - pion energy, p_T, zgg etc
   - Number of pions per run normalized by trigger for each trigger type
   
