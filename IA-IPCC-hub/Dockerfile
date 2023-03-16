FROM jupyter/base-notebook:latest

USER root
RUN apt-get update && apt-get install -y xorg git wget

USER $NB_USER
RUN conda install mamba -y -n base -c conda-forge && \
    mamba install -y -n base xarray pandas matplotlib cartopy netcdf4 cftime dask iris zarr s3fs h5py gcsfs siphon seaborn \
    nco cdo jupyter_contrib_nbextensions jupyter_nbextensions_configurator jupyter beautifulsoup4 \
    # this tensorflow is for the climate4R + tensorflow environment
    # installed here due to reticulate looking for python in the default env
    tensorflow keras

# generic environment for climate4R
# r-climate4r requires python=3.7.11
RUN conda create -n climate4r && \
    source /opt/conda/bin/activate climate4r && \
    mamba install -y -n climate4r -c conda-forge -c r -c defaults -c santandermetgroup \
        python=3.7.11 r-climate4r=2.5.3 \
        r-sf r-rgeos r-rgdal r-systemfonts r-terra r-lwgeom r-svglite r-satellite r-zip r-mapview r-dplyr r-shiny r-leafpop \
        nco cdo \
        jupyter_contrib_nbextensions jupyter_nbextensions_configurator r-irkernel && \
    R --vanilla -e 'IRkernel::installspec(name = "climate4r", displayname = "climate4R")'

# climate4R + tensorflow for deep learning
COPY c4r-tf.yml c4r-tf.yml
RUN mamba env create -n climate4tf --file c4r-tf.yml && \
    source /opt/conda/bin/activate climate4tf && \
    R --vanilla -e 'IRkernel::installspec(name = "climate4tf", displayname = "climate4R (deep)")'
