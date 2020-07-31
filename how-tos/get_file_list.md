Generating File List for STAR Data
-----------------------------------

Full reference manual: [here](https://drupal.star.bnl.gov/STAR/comp/sofi/filecatalog/user-manual)

Use the utilit `get_file_list.pl` to find file and make a file list.

Syntax: 
```
 get_file_list.pl -keys 'path,filename' -cond 'storage=XX,filetype=XX,filename~XX, production=XX,trgsetupname=XX' -limit NN -distinct -delim '/'
```
- `storage` can be `local (/home/starlib/…)`, `NFS (/star/dataXX/…)`, `HPSS (/home/starreco/reco/...)` (case insensitive) 
- `filetype` is (probably)  `daq_reco_event`, `daq_reco_muDst` or `daq_reco_picoDst` depending on whether you want to use DST, Micro DST or Pico DST reconstruted files.
  - PicoDst page is [here] (https://drupal.star.bnl.gov/STAR/blog/gnigmat/picodst-format)
- `production` and `library` can be found on data production options [page](https://www.star.bnl.gov/devcgi/dbProdOptionRetrv.pl).
- `trgsetupname` can be found on production options [page](https://www.star.bnl.gov/devcgi/dbProdOptionRetrv.pl), summary [page](https://www.star.bnl.gov/public/comp/prod/DataSummary.html) or production [page](https://drupal.star.bnl.gov/STAR/comp/prod).

A frequently used use case is 
```
$ get_file_list.pl -keys 'path,filename' -cond 'storage=hpss,filetype=daq_reco_muDst,filename~st_physics,production=P11id,trgsetupname=AuAu19_production' -limit 10 -distinct -delim '/'
```
Use `storage=local` to search file locally. 

More examples are:
```
get_file_list.pl -keys 'path,filename' -cond 'storage=hpss,filetype=daq_reco_muDst,filename~st_physics,production=P11id,trgsetupname=AuAu19_production' -limit 10 -distinct -delim '/' 

get_file_list.pl -keys path,filename -cond storage=local,trgsetupname=production_pp200trans_2015,filetype=daq_reco_mudst,filename~st_fms_16 -delim '/' 

get_file_list.pl -keys 'fdid,storage,site,node,path,filename,events' -cond 'trgsetupname=AuAu19_production, filetype=daq_reco_MuDst, filename~st_physics, storage!=hpss' -limit 60 -delim '/'. 

get_file_list.pl -keys 'path,filename' -cond 'production=P11id,filetype=daq_reco_MuDst,trgsetupname=AuAu19_production,tpx=1,filename~st_physics,sanity=1,storage!=HPSS' -limit 60 -delim '/'
```

**Note:**
- `-keys 'fdid,storage,site,node,path,filename,events'` and `-delim '/'` will dictate how (including what information) the output will be printed. Inclusion of `storage,site,node,events` will include fd (file descriptor) ID, storage (local, NFS or HPSS), node and site where the data is located and the number of events. If you do not specify `-delim` flag, theose information will be separated by `::`, other wise it will be delimited by your specification. For a typical filelist you want `-keys 'path,filename' -delim '/'`. 

- The basic idea is you always check it locally (on NFS directory) with `storage!=hpss`, if it is not available, then you try with `storage=hpss`. This is because you do not want to duplicate restoring file if it already exists. If you use, it will search under first `local`, and second under `NFS` directories.  If utility is designed to search local first, then NFS, then HPSS no matter what flag you use.

- Sometimes it is useful to enable the flag `-key 'storage, ...'` to understand if a file is really located under local, NFS or tape. Similarly, sometimes the `events` flag will tell you number of events before you restore that file.
