Bootstrap:docker  
From:ubuntu:18.04

%labels
MAINTAINER jganong@stanford.edu

%environment
### because singularity also connects to my home directory on the host,
### R packages installed there can interfere with foieGras within singularity, i get this error.
### > library(foieGras)
### Error: package or namespace load failed for ‘foieGras’:
###  .onLoad failed in loadNamespace() for 'TMB', details:
###   call: dyn.load(file, DLLpath = DLLpath, ...)
###   error: unable to load shared object '/home/jeg/R/x86_64-pc-linux-gnu-library/4.0/TMB/libs/TMB.so':
###   libopenblas.so.3: cannot open shared object file: No such file or directory
###
### here i set R_LIBS_USER to empty to avoid this issue
export R_LIBS_USER=

%runscript
exec /usr/bin/R "$@"  

%post  
echo "This singularity image was built to get Ian Jonsen's foieGras R package installed."  
echo "In email, he mentioned that he uses Ubuntu 18.04 and R 4.0.2 and foieGras just works."
echo "So I tried to recreate that, but ended up installing R 4.0.3, but that seems to work OK."
apt update
apt install --yes gnupg
apt install --yes apt-transport-https software-properties-common
export DEBIAN_FRONTEND=noninteractive DEBCONF_NONINTERACTIVE_SEEN=true
echo '
locales locales/locales_to_be_generated multiselect     en_US.UTF-8 UTF-8
locales locales/default_environment_locale      select  en_US.UTF-8
' | debconf-set-selections 
apt install --yes locales
### i figured out how to set the timezone, but singularity ignores it & uses the timezone of your host
### sigh -- but i guess that is usually what you want anyway, because the user could be far away.
### it is still useful to do all this even though it is ignored, otherwise R tries to install tzdata
### and that pops up an interactive thing in the middle of this script that you need to be noninteractive.
echo '
tzdata tzdata/Areas select Etc
tzdata tzdata/Zones/Etc select UTC
' | debconf-set-selections 
echo Etc/UTC > /etc/timezone
ln -fs /usr/share/zoneinfo/Etc/UTC /etc/localtime
apt install --yes tzdata
apt-key adv --keyserver keyserver.ubuntu.com --recv-keys E298A3A825C0D65DFD57CBB651716619E084DAB9
add-apt-repository 'deb https://cloud.r-project.org/bin/linux/ubuntu bionic-cran40/'
apt update
# these are ubuntu libraries that the foieGras R package needs to have pre-installed.
apt install --yes libproj-dev
apt install --yes libudunits2-dev
apt install --yes libgdal-dev
# install R
apt install --yes r-base
# finally, install the R package foieGras
echo "install.packages('foieGras',repos='https://cloud.r-project.org')" | R --vanilla

