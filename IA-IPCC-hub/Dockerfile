FROM jupyter/base-notebook:latest

USER root
RUN apt-get update && apt-get install -y xorg git wget && \
    mkdir -p /gpfs/ces/share-7c11c2a4-9d9f-40f5-b95e-396bcbf3f608 /home/jovyan/shared /gpfs/projects/meteo/IPCC/ATLAS && \
    chown -R jovyan:users /gpfs /home/jovyan/shared && \
    chown -R jovyan:users /home/jovyan && \
    ln -s /home/jovyan/shared /gpfs/ces/share-7c11c2a4-9d9f-40f5-b95e-396bcbf3f608/IPCC-HUB && \
    ln -s /home/jovyan/shared /gpfs/projects/meteo/IPCC/ATLAS/hub

USER jovyan

USER $NB_USER
RUN conda install mamba -y -n base -c conda-forge && \
    mamba install -y -n base xarray pandas matplotlib cartopy netcdf4 cftime dask iris zarr s3fs h5py gcsfs siphon seaborn h5netcdf vim \
    nco cdo jupyter_contrib_nbextensions jupyter_nbextensions_configurator jupyter beautifulsoup4 \
    # this tensorflow is for the climate4R + tensorflow environment
    # installed here due to reticulate looking for python in the default env
    tensorflow keras \
    python-swiftclient python-openstackclient

# generic environment for climate4R
RUN conda create -n climate4r && \
    source /opt/conda/bin/activate climate4r && \
    mamba install -y -n climate4r -c conda-forge -c r -c defaults -c santandermetgroup \
        r-climate4r=2.6.2 \
        r-sf r-rgeos r-rgdal r-systemfonts r-terra r-lwgeom r-svglite r-satellite r-zip r-mapview r-dplyr r-shiny r-leafpop r-tidyverse \
        # python for bias correction, see https://github.com/SantanderMetGroup/downscaleR/tree/devel/inst/py
	python numpy iris dask scipy pandas \
        nco cdo \
        jupyter_contrib_nbextensions jupyter_nbextensions_configurator r-irkernel && \
    R --vanilla -e 'IRkernel::installspec(name = "climate4r", displayname = "climate4R")'

# # climate4R + tensorflow for deep learning
# COPY c4r-tf.yml c4r-tf.yml
# RUN mamba env create -n climate4tf --file c4r-tf.yml && \
#     source /opt/conda/bin/activate climate4tf && \
#     R --vanilla -e 'IRkernel::installspec(name = "climate4tf", displayname = "climate4R (deep)")'
