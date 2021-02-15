Pion A<sub>N</sub> at Intermediate Rapidities With EEMC
===============================================================

Resources
-----------

- [EEMC NIM Paper](https://www.star.bnl.gov/public/tpc/NimPapers/endcap/eemc_nim.pdf) 
- [2006 PRD Paper Web Page](https://drupal.star.bnl.gov/STAR/blog/drach09/2013/feb/28/2006-eemc-neutral-pions-paper-home-page)
- [2006 Paper Link](https://journals.aps.org/prd/abstract/10.1103/PhysRevD.89.012001)
- [All Past EEMC Analyses Page](https://drupal.star.bnl.gov/STAR/book/export/html/10124)


- UCR Spin Analysis Tools Github repository: [BrightStar](https://github.com/latifkabir/BrightSTAR)

```
 Students Meeting ----> UCR Meeting ----> FMS Meeting -------> PWG Meeting -----> Working on
   	   /\ 	    	      	      	    			     	     	    feedback
     	   | 	    	      	      	    			     	     	    	|   
	   |				         					|
	   |____________________________________________________________________________|
	   

			C++ (basic syntax)
				    |
				   \|/
			     Object Oriented C++ Programming
			     	    |
				   \|/
			     Linux Commands / Unix    
				    |
				   \|/
	     User Analysis<------- ROOT<-------------------------------------------------
				    |							|
				   \|/							|						
			       STAR Analysis (StRoot)-----------------> Generate DST ---|   

```


**Excersices**

- Document `get_filelist` command
- Check that we have enough longitudinal dataset to do the analysis: total number of runs, number of events, number of fills, approximate total file sizes
- Generate DST using scheduler (Done by Latif or Xilin)
- Make basic QA plots:
     - Use macro to read DST 
     - Pion invariant mass
     - pion energy, p_T, zgg etc
     - Number of pions per run normalized by trigger for each major trigger type

- Ensure you can process all events from all files from script:
  - Separate root files by run number, use hadd to merge multiple files

  Now make the following plots:
  - Number of pions vs Run number
  - Number of EHT0 trigger vs run numbe
  - Number of pions normalized by number EHT0 trigger per run  vs run number
  - Access spin information and plot spin state for all events from all files

- Understand the pion plots:
  - Given two photon energies E1 and E2 and their opening angle, derive expression for these quantities: pion invariant mass M, E_gg, Z_gg. From the expression, try to interpret how they will change will photon/pion energy
  - From the data, plot M, E_gg, Z_gg, pion y vs pion x.
  - Apply your understanding from the derivation to make sense of the plots.



