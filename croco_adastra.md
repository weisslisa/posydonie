# Install CROCO on ADASTRA CINES

* Git clone CROCO projet from the Gitlab of Inria (assuming you have an Inria GitLab account)
* First setup ssh keys and proxy :
1) copy id_rsa.pub from ADASTRA on the Inria GitLab account
2) edit the config file on ADASTRA in ``` cd $OWN_HOMEDIR/.ssh/config``` such as : 

```
# Meom computation machine
Host ige
      Hostname ige-ssh.u-ga.fr
      User weissli

# GitHub
Host gitlab_croco
      Hostname gitlab.inria.fr
      User git
      ProxyCommand ssh ige nc %h %p
 ```
if needed see also https://docs.gitlab.com/16.8/ee/user/ssh.html

* Clone the GitLab using the ige bridge and then pass to the branch created for the stochastic developments
```
cd $WORKDIR
mkdir CROCO
git clone gitlab_croco:croco-ocean/croco.git
git clone gitlab_croco:croco-ocean/croco_tools.git

cd croco/
git checkout dev_2024_stoAR
git branch
```

## CREATE CONFIG SWIOSE

* Adjust and run ```create_config.bash```, in particular ```MY_CONFIG_NAME```

* Import the grid and all forcings from datarmor, previously loaded (Copernicus API, ERA5) and processed with ```Python_tools_export``` (see Tuto ```manga_sto.md``` - Q. Jamet)

## COMPILATION

* Prior to the compilation` - we need to set the paths to netcdf libraries.

1) Copy or point at the netcdf libraries in ```/lus/home/CT1/c1601279/lweiss/opt/hdf5_netcdf4_intel_par/``` 
2) Edit the ```.bashrc``` such as :
```
### Compile
# Intel OneAPI:
export INTEL_ONEAPI="/lus/home/softs/intel/oneapi"
export INTEL_V_COMP="2022.1.0"
export INTEL_V_MPI="2021.6.0"
#
export NCDF_INTEL="/lus/home/CT1/c1601279/lweiss/opt/hdf5_netcdf4_intel_par"
#  ==> see `load_intel()` into `.functions_lb`

# Intel compilers and MPI:
i_intel=1
if [ ${i_intel} -eq 1 ]; then
    . ${INTEL_ONEAPI}/compiler/${INTEL_V_COMP}/env/vars.sh
    . ${INTEL_ONEAPI}/mpi/${INTEL_V_MPI}/env/vars.sh
    export LD_LIBRARY_PATH=${NCDF_INTEL}/lib:${LD_LIBRARY_PATH}
fi

### modules
module load PrgEnv-intel
module load cray-hdf5-parallel/1.12.2.5
module load cray-parallel-netcdf/1.12.3.5
module list
```
3) Create a link to source the .bashrc at the login : ```ln -sf .bashrc .profile```


* Move to your configuration directory ```cd ./CONFIG/SWIOSE/```

* Create a compilation script name ```compilation``` with :
```
#!/bin/bash

# set appropriate modules
source /lus/home/PERSO/grp_lweiss/lweiss/.bashrc

STARTTIME=$(date +%s)
./jobcomp
ENDTIME=$(date +%s)
ELAPSETIME=$(( $ENDTIME - $STARTTIME ))
echo "Elapse Time: $ELAPSETIME sec"
```

* Edit ```cppdefs.h``` with the name and options of our regional configuration :
```
# define REGIONAL
# define SWIOSE
# define  MPI          -> to run in parallel
# define  TIDES
# define USE_CALENDAR
# define NC4PAR        -> deal with parallel I/O
# define MPI_NOLAND    -> deal with dry MPI proc (see ./croco/MPI_NOLAND for details)
# define WET_DRY`      -> deal with drying grid cell with tides/high pressure
# undef LMD_MIXING
# define  GLS_MIXING   -> change vertical mixing scheme from KPP to GLS-KEPSILON
# define PSOURCE       -> deal with river runoff 
```

* Edit ```param.h``` : define the size of your domain
```
#elif defined REGIONAL
# if defined  BENGUELA_LR
      parameter (LLm0=41,   MMm0=42,   N=32)   ! BENGUELA_LR
# elif defined  SWIOSE
      parameter (LLm0=370,   MMm0=460,   N=50)   ! YOUR_CONF_NAME
```

* Edit ```jobcomp``` such as :
```
FC=ifort
NETCDFLIB="-L/lus/home/CT1/c1601279/lweiss/opt/hdf5_netcdf4_intel_par/lib -lnetcdf -lnetcdff -lhdf5_hl -lhdf5 -lhdf5 -L/usr/lib64 -lz -lcurl -lstdc++"
NETCDFINC="-I/lus/home/CT1/c1601279/lweiss/opt/hdf5_netcdf4_intel_par/include"
```

* Run ```./compile```

* If compilation is successful, you should have a ```croco``` executable in your directory.

## RUN

* Atmospheric forcing *ONLINE*

in 'CROCO/RUN/SWIOSE/DATA/ERA5_SWIOSE' do 'mv [var]_Y2019M1.nc [var]_Y2019M01.nc'
and then :  
```
ncatted -a time,units,o,c,"days since 2000-01-01" copy_[var]_Y2019M01.nc out_[var]_Y2019M01.nc'
```

