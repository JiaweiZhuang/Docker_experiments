FROM gc_env
MAINTAINER Jiawei Zhuang <jiaweizhuang@g.harvard.edu>

# build-time arguments, not used in run-time
ARG GC_VERSION=12.1.0 
ARG UT_VERSION=12.1.0 

# Do not need `mkdir`
WORKDIR /GC_tutorial
RUN git clone https://github.com/geoschem/geos-chem.git Code.GC \
    && cd Code.GC && git checkout $GC_VERSION

# We are back to WORKDIR after RUN
RUN git clone https://github.com/geoschem/geos-chem-unittest.git UT \
    && cd UT && git checkout $UT_VERSION

# Input data path as required by UT
# Need to mount real data volume at run-time
RUN mkdir -p /Extdata/HEMCO


# Set default command so the following two commands are equivalent
# $ docker run --rm -it gc_env
# $ docker run --rm -it gc_env /bin/bash
CMD ["/bin/bash"]
