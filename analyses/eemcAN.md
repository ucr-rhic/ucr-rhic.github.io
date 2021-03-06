Pion A<sub>N</sub> at Intermediate Rapidities With EEMC
===============================================================

Resources
-----------

- [EEMC NIM Paper](https://www.star.bnl.gov/public/tpc/NimPapers/endcap/eemc_nim.pdf) 
- [2006 PRD Paper Web Page](https://drupal.star.bnl.gov/STAR/blog/drach09/2013/feb/28/2006-eemc-neutral-pions-paper-home-page)
- [2006 Paper Link](https://journals.aps.org/prd/abstract/10.1103/PhysRevD.89.012001)
- [All Past EEMC Analyses Page](https://drupal.star.bnl.gov/STAR/book/export/html/10124)


- UCR Spin Analysis Tools Github repository: [BrightStar](https://github.com/latifkabir/BrightSTAR)
- Calss references for EEMC pion Tree: [here](https://www.star.bnl.gov/webdata/dox/html/dir_f55c6bc819b98feb2a7a93228ad627a8.html)
- Class reference for EEMC DST event information:[here](https://github.com/latifkabir/BrightSTAR/blob/master/StRoot/BrContainers/TStEventData.h) 


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

How to generate DST files
-----------------------------

- Download and compile `BrightSTAR` analysis library on RCF following the instructions [here](https://github.com/latifkabir/BrightSTAR).
- Modify `sumsJob.xml` file to update your trigger/dataset information and various paths.
- Replace the function called in `jobMacro` with `RunEEmcNanoDstMaker`.
- Ensure `config/config.cgf` has `ENABLE_EEMC` and `ENABLE_EVT` set to 1 and other detectors to 0.
- Submit the jobs using STAR scheduler as: `star-submit sumsJobs.xml`


How to read the DST
---------------------

- Follow this example [here](https://github.com/latifkabir/BrightSTAR/blob/master/studentsAna/scripts/ReadEEmcNanoTree.C).


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
  - Number of EHT0 trigger vs run number
  - Number of pions normalized by number EHT0 trigger per run  vs run number
  - Access spin information and plot spin state for all events from all files

- Understand the pion plots:
  - Given two photon energies E1 and E2 and their opening angle, derive expression for these quantities: pion invariant mass M, Z_gg, d_gg. From the expression, try to interpret how they will change will photon/pion energy
  - From the data, plot M, Z_gg, d_gg, p_t, E, P, pion y vs pion x, pion eta, pion phi.
  - Apply your understanding from the derivation to make sense of the plots.
  - Use photon candidates from the photon branch to calculate pion invariant mass and compare it with pion invariant mass from pion branch.

- Towards Asymmetry Calculations:
  - Make a plot showing trigger distributions among all events
  - Take the two leading triggers from your trigger distribution and for those two triggers plot pion invariant mass. Normalized the plots and super impose those two pion invariant mass plots on each other to compare them.
  - Make one or two slides explaining how to calculate A_N and list any variables that you do not have in your analysis tree


