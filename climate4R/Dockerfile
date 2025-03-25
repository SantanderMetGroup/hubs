FROM jupyter/base-notebook:latest

ARG DEBIAN_FRONTEND=noninteractive
ENV TZ=Etc/UTC

USER root
RUN apt-get update && apt-get install -y xorg git wget build-essential tzdata && apt-get clean && \
    mkdir -p /gpfs/ces/share-7c11c2a4-9d9f-40f5-b95e-396bcbf3f608 /home/$NB_USER/shared /gpfs/projects/meteo/IPCC/ATLAS && \
    chown -R $NB_USER:users /gpfs /home/$NB_USER/shared && \
    chown -R jovyan:users /home/jovyan && \
    ln -s /home/$NB_USER/shared /gpfs/ces/share-7c11c2a4-9d9f-40f5-b95e-396bcbf3f608/HUB && \
    ln -s /home/$NB_USER/shared /gpfs/projects/meteo/IPCC/ATLAS/hub

USER jovyan
RUN conda install mamba -y -n base -c conda-forge && \
    # https://github.com/proxystore/taps/issues/151#issuecomment-2340406425 \
    mamba install -y -n base jupyter 'libsqlite<3.46' nbgitpuller jupyterhub-idle-culler jupyterlab-git \
    # this tensorflow is for the climate4R + tensorflow environment, installed here due to reticulate looking for python in the default env
    tensorflow keras

## python environment with xarray etc
#RUN conda create -n xarray && \
#    source /opt/conda/bin/activate xarray && \
#    mamba install -y jupyter xarray pandas matplotlib cartopy netcdf4 cftime dask iris zarr s3fs h5py gcsfs siphon beautifulsoup4 scikit-learn geopandas h5netcdf python-swiftclient \
#    nco cdo cf-python cf-plot && \
#    python -m ipykernel install --name xarray --display-name "xarray" --prefix /opt/conda

# python environment with netcdf 4.8 etc
RUN conda create -n xarray && \
    source /opt/conda/bin/activate xarray && \
    mamba install -y 'libnetcdf<4.9' jupyter xarray pandas matplotlib cartopy netcdf4 cftime dask iris zarr s3fs h5py gcsfs siphon beautifulsoup4 scikit-learn geopandas h5netcdf python-swiftclient \
    nco cdo cf-python cf-plot && \
    python -m ipykernel install --name xarray --display-name "xarray" --prefix /opt/conda

# generic environment for climate4R
# r-climate4r requires python=3.7.11
RUN conda create -n climate4r && \
    source /opt/conda/bin/activate climate4r && \
    mamba install -y -n climate4r -c conda-forge -c r -c defaults -c santandermetgroup \
        r-climate4r jupyter \
        r-sf r-rgeos r-rgdal r-systemfonts r-terra r-lwgeom r-svglite r-satellite r-zip r-mapview r-dplyr r-shiny r-leafpop \
        r-curl r-httr \
        nco cdo \
        jupyter_contrib_nbextensions jupyter_nbextensions_configurator r-irkernel && \
    R --vanilla -e 'IRkernel::installspec(name = "climate4r", displayname = "climate4R", user = FALSE, prefix="/opt/conda")'

# create conda env for FAO due to custom packages
# oceanis for FAO users requires: r-sf r-systemfonts r-terra r-lwgeom r-svglite r-satellite r-zip r-mapview r-dplyr r-shiny r-leafpop
RUN conda create -n cava && \
    source /opt/conda/bin/activate cava && \
    mamba clean --all -y && \
    mamba install -y -c conda-forge -c r -c santandermetgroup \
        jupyter r-irkernel r-devtools \
        r-downscaleR r-abind r-loadeR r-transformeR r-geoprocessoR r-raster \
        r-leaflet r-htmltools r-furrr r-tidyverse r-sp r-png r-gridExtra \
        r-climate4R.indices r-rnaturalearth r-rnaturalearthdata r-leafsync \
        r-parallelly r-dtplyr r-mapview r-patchwork r-RColorBrewer r-glue \
	'r-sf>=1.0_14,<=1.0_17' r-rgeos r-rgdal r-systemfonts r-terra r-lwgeom r-svglite r-satellite r-zip r-mapview r-dplyr r-shiny r-leafpop \
        r-plotly r-broom r-loadeR.2nc r-ggnewscale r-shiny r-geojsonio \
	# 2023-05-11 \
	r-future r-tidyr r-ggh4x r-reshape2 r-patchwork r-magrittr \
	icu && \
    R --vanilla -e 'install.packages("leaflegend", repos="http://cran.us.r-project.org")' && \
    R --vanilla -e 'install.packages("oceanis", repos="http://cran.us.r-project.org")' && \
    R --vanilla -e 'install.packages("CopernicusMarine", repos="http://cran.us.r-project.org")' && \
    R --vanilla -e 'install.packages("mvabund", repos="http://cran.us.r-project.org")' && \
    R --vanilla -e 'install.packages("stxplore", repos="http://cran.us.r-project.org")' && \
    R --vanilla -e 'library(devtools); devtools::install_github("ropensci/rnaturalearthhires")' && \
    R --vanilla -e 'library(devtools); devtools::install_github("Risk-Team/CAVAanalytics", dependencies=FALSE)' && \
    R --vanilla -e 'IRkernel::installspec(name = "cava", displayname = "CAVA Analytics", user = FALSE, prefix="/opt/conda")'

## SUNSET from BSC (https://earth.bsc.es/gitlab/es/sunset/-/tree/master)
#RUN git clone https://earth.bsc.es/gitlab/es/sunset.git && \
#    mamba env create -n SUNSET --file sunset/conda_installation/environment-sunset.yml -y && \
#    source /opt/conda/bin/activate SUNSET && \
#    mamba install -c conda-forge -y r-irkernel && \
#    R --vanilla -e "options(timeout = 600) ; install.packages('CSTools', repos='https://ftp.cixug.es/CRAN/')" && \
#    R --vanilla -e "options(timeout = 600) ; install.packages('CSIndicators', repos='https://ftp.cixug.es/CRAN/')" && \
#    R --vanilla -e 'IRkernel::installspec(name = "sunset", displayname = "SUNSET")'

# pyAEZ for FAO
RUN conda create -n pyAEZ && \
    source /opt/conda/bin/activate pyAEZ && \
    mamba clean --all -y && \
    mamba install -y -c conda-forge jupyter pyAEZ ipykernel && \
    python -m ipykernel install --name pyAEZ --display-name "pyAEZ" --prefix /opt/conda


# climate4R + tensorflow for deep learning
COPY c4r-tf.yml c4r-tf.yml
RUN mamba env create -n climate4tf --file c4r-tf.yml && \
    source /opt/conda/bin/activate climate4tf && \
    mamba install -y -c conda-forge -c r -c santandermetgroup jupyter && \
    R --vanilla -e 'IRkernel::installspec(name = "climate4tf", displayname = "climate4R (deep)", user = FALSE)'
