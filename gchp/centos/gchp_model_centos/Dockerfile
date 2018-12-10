FROM zhuangjw/gchp_env_centos
LABEL maintainer="Jiawei Zhuang <jiaweizhuang@g.harvard.edu>"

# Git tag or commit
ARG GC_VERSION=b544322
ARG GCHP_VERSION=12.1.0

# Input & output data path
# Need to mount real data volume at run-time
RUN mkdir /ExtData && mkdir /OutputDir

WORKDIR /tutorial

# Source code
RUN git clone https://github.com/geoschem/geos-chem.git Code.GCHP \
    && cd Code.GCHP && git checkout $GC_VERSION \
    && git clone https://github.com/geoschem/gchp.git GCHP \
    && cd GCHP && git checkout $GCHP_VERSION \
    && cd Run && printf "/ExtData \n 2 \n 1 \n /tutorial \n gchp_standard \n n" | ./createRunDir.sh

WORKDIR gchp_standard

# compile source code
RUN touch gchp.env && make compile_clean

CMD ["/bin/bash"]