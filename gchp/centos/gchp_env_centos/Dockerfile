FROM centos:centos7
LABEL maintainer="Jiawei Zhuang <jiaweizhuang@g.harvard.edu>"

# The default compiler is gfortran4 but GCHP requires gfortran7+
RUN yum -y update \
    && yum install -y make wget vim git m4 bzip2 bc numpy-f2py \
    && yum install -y http://mirror.centos.org/centos/7/sclo/x86_64/rh/devtoolset-7/devtoolset-7-runtime-7.1-4.el7.x86_64.rpm \
    && yum install -y https://buildlogs.centos.org/centos/7/sclo/x86_64/rh/devtoolset-7/devtoolset-7-binutils-2.28-7.el7.x86_64.rpm \
    && yum install -y https://buildlogs.centos.org/centos/7/sclo/x86_64/rh/devtoolset-7/devtoolset-7-gcc-7.2.1-1.el7.x86_64.rpm \
    && yum install -y https://buildlogs.centos.org/centos/7/sclo/x86_64/rh/devtoolset-7/devtoolset-7-libquadmath-devel-7.2.1-1.el7.x86_64.rpm \
    && yum install -y https://buildlogs.centos.org/centos/7/sclo/x86_64/rh/devtoolset-7/devtoolset-7-gcc-gfortran-7.2.1-1.el7.x86_64.rpm \
    && yum install -y https://buildlogs.centos.org/centos/7/sclo/x86_64/rh/devtoolset-7/devtoolset-7-libstdc++-devel-7.2.1-1.el7.x86_64.rpm \
    && yum install -y https://buildlogs.centos.org/centos/7/sclo/x86_64/rh/devtoolset-7/devtoolset-7-gcc-c++-7.2.1-1.el7.x86_64.rpm \
    && yum clean all

# Set gcc7 as default compiler to build libraries
ENV PATH=/opt/rh/devtoolset-7/root/usr/bin:$PATH
ENV CC=gcc CXX=g++ FC=gfortran F77=gfortran F90=gfortran

# build libraries from source
ARG installDir=/usr
ARG ZDIR=$installDir
ARG H5DIR=$installDir
ARG NCDIR=$installDir
ARG NFDIR=$installDir

# temporarily hosting library source code
# clean up tmp directory inside every RUN command to reduce layer size
WORKDIR /tmp

# Install zlib
RUN wget https://zlib.net/zlib-1.2.11.tar.gz \
    && tar -xzf zlib-1.2.11.tar.gz \
    && cd zlib-1.2.11 \
    && ./configure --prefix=${ZDIR} \
    && make -j4 \
    && make install \
    && rm -rf /tmp/*

# Install HDF5
RUN wget https://support.hdfgroup.org/ftp/HDF5/releases/hdf5-1.10/hdf5-1.10.2/src/hdf5-1.10.2.tar.gz \
    && tar -xzf hdf5-1.10.2.tar.gz \
    && cd hdf5-1.10.2 \
    && ./configure --with-zlib=${ZDIR} --prefix=${H5DIR} \
    && make -j4 \
    && make install \
    && rm -rf /tmp/*

# Install NetCDF-C
RUN wget https://github.com/Unidata/netcdf-c/archive/v4.6.1.tar.gz \
    && tar -xzf v4.6.1.tar.gz \
    && cd netcdf-c-4.6.1 \
    # don't need remote access
    && CPPFLAGS=-I${H5DIR}/include LDFLAGS=-L${H5DIR}/lib ./configure --prefix=${NCDIR} --disable-dap \
    && make -j4 \
    && make install \
    && rm -rf /tmp/*

# Install NetCDF-Fortran
RUN export LD_LIBRARY_PATH=${NCDIR}/lib:${LD_LIBRARY_PATH} \
    && wget https://github.com/Unidata/netcdf-fortran/archive/v4.4.4.tar.gz \
    && tar -xzf v4.4.4.tar.gz \
    && cd netcdf-fortran-4.4.4 \
    && CPPFLAGS=-I${NCDIR}/include LDFLAGS=-L${NCDIR}/lib ./configure --prefix=${NFDIR} \
    && make -j4 \
    && make install \
    && rm -rf /tmp/*

# Install MPICH
RUN wget http://www.mpich.org/static/downloads/3.2.1/mpich-3.2.1.tar.gz \
    && tar zxf mpich-3.2.1.tar.gz \
    && cd mpich-3.2.1 \
    && unset F90 \
    && ./configure prefix=/usr/local/mpich \
    && make -j4 \
    && make install \
    && rm -rf /tmp/*

# mpicc, mpifort
ENV PATH=/usr/local/mpich/bin:$PATH

# GEOS-Chem classic environment
ENV NETCDF_HOME=$NCDIR NETCDF_FORTRAN_HOME=$NFDIR
ENV GC_BIN=$NETCDF_HOME/bin \
    GC_INCLUDE=$NETCDF_HOME/include \
    GC_LIB=$NETCDF_HOME/lib \
    GC_F_BIN=$NETCDF_FORTRAN_HOME/bin \
    GC_F_INCLUDE=$NETCDF_FORTRAN_HOME/include \
    GC_F_LIB=$NETCDF_FORTRAN_HOME/lib

# for libnetcdff.so
ENV LD_LIBRARY_PATH=$NFDIR/lib:$LD_LIBRARY_PATH

# GCHP-specific environment
ENV OMPI_CC=$CC OMPI_CXX=$CXX OMPI_FC=$FC \
    COMPILER=$FC ESMF_COMPILER=$FC \
    MPI_ROOT=/usr/local/mpich \
    ESMF_COMM=mpich2 \
    ESMF_BOPT=O

# fix Perl error message
ENV LANG C

WORKDIR /root