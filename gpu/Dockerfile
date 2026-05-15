# =============================================================================
# DSN GPU Docker Image - Ubuntu 24.04 + CUDA 13.2
# =============================================================================

FROM nvidia/cuda:13.2.0-devel-ubuntu24.04

MAINTAINER Danny Luong "dannyluong@live.com"

ENV DEBIAN_FRONTEND=noninteractive \
    TZ=UTC \
    LANG=C.UTF-8

USER root
RUN echo "root:foobar2026" | chpasswd

RUN apt update && apt install -y --no-install-recommends \
    build-essential \
    automake \ 
    autoconf \
    libtool \
    cmake \
    git \
    wget \
    curl \
    vim \
    csh \
    gfortran \
    pgplot5 \
    meson \
    libcfitsio-dev \
    ca-certificates \
    pkg-config \
    sqlite3 \
    libsqlite3-dev \
    libboost-all-dev \
    libxml2-dev \
    libxslt1-dev \
    libssl-dev \
    libcurl4-openssl-dev \
    libpng-dev \
    libjpeg-dev \
    libtiff-dev \
    libopenexr-dev \
    libgeos-dev \
    libproj-dev \
    libspatialite-dev \
    libfreexl-dev \
    libprotobuf-dev \
    protobuf-compiler \
    libhdf5-dev \
    libnetcdf-dev \
    libudunits2-dev \
    libfftw3-dev \
    libgsl-dev \
    liblapack-dev \
    libblas-dev \
    libeigen3-dev \
    libgl-dev \
    libglu1-mesa-dev \
    libosmesa6-dev \
    libx11-dev \
    libxmu-dev \
    libxi-dev \
    libxinerama-dev \
    libxcursor-dev \
    libxrandr-dev \
    libxft-dev \
    libfontconfig1-dev \
    libfreetype6-dev \
    libharfbuzz-dev \
    libpango1.0-dev \
    libcairo2-dev \
    libatk1.0-dev \
    libgtk-3-dev \
    libncurses-dev \
    libreadline-dev \
    libedit-dev \
    libgmp-dev \
    libmpfr-dev \
    libmpc-dev \
    libgtest-dev \
    && rm -rf /var/lib/apt/lists/*

# Build and install GTest (helps PROJ)
RUN cd /usr/src/googletest && \
    cmake . && \
    cmake --build . --target install && \
    ldconfig

# Build PROJ from source
ARG PROJ_VERSION=9.8.0
RUN wget -q https://download.osgeo.org/proj/proj-${PROJ_VERSION}.tar.gz && \
    tar -xzf proj-${PROJ_VERSION}.tar.gz && \
    cd proj-${PROJ_VERSION} && \
    mkdir build && cd build && \
    cmake .. -DCMAKE_BUILD_TYPE=Release \
             -DCMAKE_INSTALL_PREFIX=/usr/local \
             -DENABLE_TIFF=ON \
             -DENABLE_CURL=ON \
             -DBUILD_TESTING=OFF && \
    make -j$(nproc) && \
    make install && \
    ldconfig && \
    cd ../.. && rm -rf proj-${PROJ_VERSION}*

# Build GDAL from source
ARG GDAL_VERSION=3.12.3
RUN wget -q https://github.com/OSGeo/gdal/releases/download/v${GDAL_VERSION}/gdal-${GDAL_VERSION}.tar.gz && \
    tar -xzf gdal-${GDAL_VERSION}.tar.gz && \
    cd gdal-${GDAL_VERSION} && \
    mkdir build && cd build && \
    cmake .. -DCMAKE_BUILD_TYPE=Release \
             -DCMAKE_INSTALL_PREFIX=/usr/local \
             -DGDAL_USE_GEOS=ON \
             -DGDAL_USE_PROJ=ON \
             -DGDAL_USE_SQLITE3=ON \
             -DGDAL_USE_CURL=ON \
             -DGDAL_USE_OPENEXR=ON \
             -DGDAL_USE_HDF5=ON \
             -DGDAL_USE_NETCDF=ON && \
    make -j$(nproc) && \
    make install && \
    ldconfig && \
    cd ../.. && rm -rf gdal-${GDAL_VERSION}*

ENV GDAL_DATA=/usr/local/share/gdal \
    PROJ_DATA=/usr/local/share/proj \
    LD_LIBRARY_PATH=/usr/local/lib:${LD_LIBRARY_PATH}

WORKDIR /app

# Install miniforge conda python
# Ubuntu-24.04 no longer allows using pip to install packages to system python
RUN wget https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-Linux-x86_64.sh -O miniforge.sh && \
chmod +x miniforge.sh && \
./miniforge.sh -b -p /opt/miniforge3 && \
rm -f miniforge.sh && \
chown -R root:root /opt/miniforge3 && \
chmod -R a+rX /opt/miniforge3 && \
export PATH="/opt/miniforge3/bin:$PATH" && \
. /opt/miniforge3/etc/profile.d/conda.sh && \
conda update -y conda && \
conda create -n py312 python=3.12 -y && \
    update-alternatives --install /usr/bin/python  python  /opt/miniforge3/envs/py312/bin/python  1 && \
    update-alternatives --install /usr/bin/python3 python3 /opt/miniforge3/envs/py312/bin/python  2 && \
    update-alternatives --set python  /opt/miniforge3/envs/py312/bin/python && \
    update-alternatives --set python3 /opt/miniforge3/envs/py312/bin/python

# Install python modules
RUN python3 -m pip install pip -U && \
    python3 -m pip install setuptools -U && \
    python3 -m pip install datetime -U && \
    python3 -m pip install bitstring -U && \
    python3 -m pip install ipython -U && \
    python3 -m pip install --ignore-installed jupyter -U && \
    python3 -m pip install six -U && \
    python3 -m pip install numpy -U && \
    python3 -m pip install scipy -U && \
    python3 -m pip install pandas -U && \
    python3 -m pip install h5py -U && \
    python3 -m pip install astropy -U && \
    python3 -m pip install astroplan -U && \
    #python3 -m pip install astropy_helpers -U && \
    python3 -m pip install astroquery -U && \
    python3 -m pip install pytz -U && \
    #python3 -m pip install paramz -U && \
    #python3 -m pip install APLpy -U && \
    #python3 -m pip install pyfits -U && \
    python3 -m pip install matplotlib -U && \
    python3 -m pip install pyephem -U && \
    python3 -m pip install setuptools_scm pep517 -U && \
    python3 -m pip install emcee -U && \
    python3 -m pip install scikit-learn -U && \
    python3 -m pip install corner -U && \
    python3 -m pip install psrqpy -U && \
    python3 -m pip install uncertainties -U && \
    python3 -m pip install getdist -U && \
    python3 -m pip install dynesty -U && \
    python3 -m pip install dyPolyChord -U && \
    python3 -m pip install lmfit -U && \
    python3 -m pip install PyWavelets -U && \
    python3 -m pip install numba -U && \
    python3 -m pip install pint-pulsar -U && \
    python3 -m pip install numdifftools -U && \
    python3 -m pip install your && \
    python3 -m pip install click && \
    python3 -m pip install testresources -U && \
    python3 -m pip install typing-extensions -U  && \
    #python3 -m pip install 'protobuf~=3.19.0' && \
    python3 -m pip install protobuf -U && \
    #python3 -m pip install tensorflow==2.5 && \
    #python3 -m pip install tensorflow-gpu==2.5 && \
    #python3 -m pip install keras==2.4  && \
    #python3 -m pip install rich==12.0
    python3 -m pip install rich -U

# Define home, psrhome, OSTYPE and create the directory
ENV HOME /home/psr
ENV PSRHOME /home/psr/software
ENV OSTYPE linux
RUN mkdir -p /home/psr/software

# Define GPU env var which sets the compatability
# code for your GPU.  This is the number after "sm_" in
# e.g., "sm_60".
ENV GPU 86

# Downloading all source codes
WORKDIR $PSRHOME
RUN wget --no-check-certificate https://www.imcce.fr/content/medias/recherche/equipes/asd/calceph/calceph-2.3.2.tar.gz && tar -xvvf calceph-2.3.2.tar.gz -C $PSRHOME && \
    wget --no-check-certificate https://ds9.si.edu/download/ubuntu24x86/ds9.ubuntu24x86.8.7.tar.gz && mkdir -p $PSRHOME/ds9-8.7 && tar -xvvf ds9.ubuntu24x86.8.7.tar.gz -C $PSRHOME/ds9-8.7 && \
    wget --no-check-certificate https://heasarc.gsfc.nasa.gov/FTP/software/lheasoft/fv/fv5.5.3_src.tar.gz && tar -xvvf fv5.5.3_src.tar.gz -C $PSRHOME && \
    wget --no-check-certificate http://www.atnf.csiro.au/people/pulsar/psrcat/downloads/psrcat_pkg.tar.gz && tar -xvf psrcat_pkg.tar.gz -C $PSRHOME && \
    wget --no-check-certificate https://support.hdfgroup.org/ftp/lib-external/szip/2.1.1/src/szip-2.1.1.tar.gz && tar -xvvf szip-2.1.1.tar.gz && \
    wget --no-check-certificate https://support.hdfgroup.org/ftp/HDF5/tools/h5check/src/h5check-2.0.1.tar.gz && tar -xvvf h5check-2.0.1.tar.gz && \
    git clone https://github.com/SixByNine/psrxml.git && \
    git clone https://bitbucket.org/psrsoft/tempo2.git && \
    git clone https://github.com/gdesvignes/tempo.git && \
    git clone https://github.com/rwharton/prepfil_dsn.git prepfil && \
    git clone git://git.code.sf.net/p/psrchive/code psrchive && \
    git clone https://github.com/rwharton/sigproc_dsn_mod.git sigproc && \
    git clone https://github.com/ewanbarr/sigpyproc.git && \
    git clone https://git.code.sf.net/p/dspsr/code dspsr && \
    git clone https://github.com/weltevrede/psrsalsa.git && \
    git clone https://github.com/rwharton/presto.git && \
    #git clone https://github.com/scottransom/presto.git && \
    git clone https://github.com/scottransom/psrfits2psrfits.git && \
    git clone https://github.com/gdesvignes/psrfits_utils.git && \
    git clone https://github.com/scottransom/pyslalib.git && \
    git clone https://github.com/straten/epsic.git && \
    git clone https://github.com/JohannesBuchner/MultiNest && \
    git clone https://github.com/PolyChord/PolyChordLite.git && \
    git clone https://github.com/gdesvignes/TempoNest.git && \
    git clone https://github.com/vivekvenkris/plotres.git && \
    git clone https://github.com/vivekvenkris/fitorbit.git && \
    git clone https://github.com/gdesvignes/PulsePortraiture.git && \
    git clone https://github.com/demorest/tempo_utils && \
    #git clone https://github.com/rwharton/dedisp-dsn.git dedisp && \
    git clone https://github.com/ajameson/dedisp.git && \
    git clone git://git.code.sf.net/p/psrdada/code psrdada && \
    git clone https://git.code.sf.net/p/heimdall-astro/code heimdall && \
    git clone https://github.com/devanshkv/fetch.git fetch && \
    git clone --recursive https://github.com/sorin-ionescu/prezto.git "${ZDOTDIR:-$HOME}/.zprezto"
#    rm -f *.tar.gz *.tar.xz


######################################
####  DSN SOFTWARE INSTALLATIONS  ####
######################################

# PGPLOT
ENV PGPLOT_DIR="/usr/lib/pgplot5" \
    PGPLOT_FONT="/usr/lib/pgplot5/grfont.dat" \
    PGPLOT_INCLUDES="/usr/include" \
    PGPLOT_BACKGROUND="white" \
    PGPLOT_FOREGROUND="black" \
    PGPLOT_DEV="/xs"

# calceph
#ENV CALCEPH=$PSRHOME"/calceph-2.3.2" \
#    PATH=$PATH:$PSRHOME"/calceph-2.3.2/install/bin" \
#    LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$PSRHOME"/calceph-2.3.2/install/lib" \
#    C_INCLUDE_PATH=$C_INCLUDE_PATH:$PSRHOME"/calceph-2.3.2/install/include"
#WORKDIR $CALCEPH
#RUN ./configure --prefix=$CALCEPH/install --with-pic --enable-shared --enable-static --enable-fortran --enable-thread && \
#    make && \
#    make check && \
#    make install

# ds9
ENV PATH $PATH:$PSRHOME/ds9-8.7

# fv
ENV PATH $PATH:$PSRHOME/fv5.5.3

# psrcat
ENV PSRCAT_FILE=$PSRHOME"/psrcat_tar/psrcat.db" \
    PATH=$PATH:$PSRHOME"/psrcat_tar"
WORKDIR $PSRHOME/psrcat_tar
RUN /bin/bash makeit

# psrXML
ENV PSRXML=$PSRHOME"/psrxml" \
    PATH=$PATH:$PSRHOME"/psrxml/install/bin" \
    LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$PSRHOME"/psrxml/install/lib" \
    C_INCLUDE_PATH=$C_INCLUDE_PATH:$PSRHOME"/psrxml/install/include"
WORKDIR $PSRXML
RUN libtoolize --copy --force
RUN autoreconf --install --warnings=none
RUN ./configure --prefix=$PSRXML/install && \
    make && \
    make install

# tempo
ENV TEMPO=$PSRHOME"/tempo" \
    PATH=$PATH:$PSRHOME"/tempo/bin"
WORKDIR $PSRHOME/tempo
RUN ./prepare && \
    ./configure --prefix=$PSRHOME/tempo && \
    make && \
    make install
# Add DSN obs to obsys.dat
RUN echo "-4460894.947   2682367.17     -3674745.22      1  CANBERRA            n  CN" >> obsys.dat && \
    echo "-2353621.22    -4641341.52     3677052.352     1  GOLDSTONE           q  GS" >> obsys.dat && \ 
    echo " 4849093.992    -360191.27      4115107.66     1  ROBLEDO             j  RO" >> obsys.dat

# tempo2
ENV TEMPO2=$PSRHOME"/tempo2/T2runtime" \
    PATH=$PATH:$PSRHOME"/tempo2/T2runtime/bin" \
    C_INCLUDE_PATH=$C_INCLUDE_PATH:$PSRHOME"/tempo2/T2runtime/include" \
    LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$PSRHOME"/tempo2/T2runtime/lib"
WORKDIR $PSRHOME/tempo2
# A fix to get rid of: returned a non-zero code: 126.
RUN sync && perl -pi -e 's/chmod \+x/#chmod +x/' bootstrap
RUN ./bootstrap && \
    ./configure --x-libraries=/usr/lib/x86_64-linux-gnu --enable-shared --enable-static --with-pic F77=gfortran  && \
    make -j $(nproc) && \
    make install && \
    make plugins-install
WORKDIR $PSRHOME/tempo2/T2runtime/clock
RUN touch meerkat2gps.clk && \
    echo "# UTC(meerkat) UTC(GPS)" > meerkat2gps.clk && \
    echo "#" >> meerkat2gps.clk && \
    echo "50155.00000 0.0" >> meerkat2gps.clk && \
    echo "58000.00000 0.0" >> meerkat2gps.clk
WORKDIR $PSRHOME/tempo2/T2runtime/observatory
RUN sed -i -e '/New telescopes/a\' -e ' -2353621.     -4641341.4       3677052.2        DSS28               dss28' observatories.dat && \
    sed -i -e '/New telescopes/a\' -e ' -4460894.947   2682367.17     -3674745.22       CANBERRA            cn' observatories.dat && \
    sed -i -e '/New telescopes/a\' -e ' 4849093.992    -360191.27      4115107.66       ROBLEDO             ro' observatories.dat 

# PREPFIL
WORKDIR $PSRHOME/prepfil/filterbank_utils
RUN sed -i "/gencode/c\    set(CUDA_NVCC_FLAGS \${CUDA_NVCC_FLAGS};-gencode arch=compute_${GPU},code=sm_${GPU})" CMakeLists.txt && \
    # Fix deprecated CUDA headers \
    sed -i 's/case CUFFT_INCOMPLETE_PARAMETER_LIST/\/\/case/g' src/CUDA.hpp && \
    sed -i 's/case CUFFT_PARSE_ERROR/\/\/case/g' src/CUDA.hpp && \
    mkdir build
WORKDIR $PSRHOME/prepfil/filterbank_utils/build
RUN cmake .. -DCMAKE_INSTALL_PREFIX=$PSRHOME/prepfil/filterbank_utils/install -DCUDA_NVCC_FLAGS="-std=c++17;-DCCCL_IGNORE_DEPRECATED_CPP_DIALECT" -DCMAKE_CXX_STANDARD=17 && \
    make -j$(nproc) && \
    make install

# EPSIC
ENV EPSIC=$PSRHOME"/epsic"
WORKDIR $EPSIC/src
RUN ./bootstrap && \
    ./configure --prefix=$PSRHOME"/epsic/install" && make -j $(nproc) && make &&  make install > test
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$PSRHOME"/epsic/install/lib" \
    C_INCLUDE_PATH=$C_INCLUDE_PATH:$PSRHOME"/epsic/install/include/epsic" \
    PATH=$PATH:$PSRHOME"/epsic/install/bin" \
    CPPFLAGS="-I ${PSRHOME}/epsic/install/include/epsic"  \
    CFLAGS="-I ${PSRHOME}/epsic/install/include/epsic" 

# PSRCHIVE
ENV PSRCHIVE=$PSRHOME"/psrchive/install" \
    PATH=$PATH:$PSRHOME"/psrchive/install/bin" \
    C_INCLUDE_PATH=$C_INCLUDE_PATH:$PSRHOME"/psrchive/install/include" \
    LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$PSRHOME"/psrchive/install/lib" \
    PYTHONPATH=$PYTHONPATH:$PSRHOME"/psrchive/install/lib/python3.8/site-packages"
WORKDIR $PSRHOME/psrchive/
RUN apt update && apt install swig -y
RUN ./bootstrap && \
    ./configure --prefix=$PSRCHIVE --x-libraries=/usr/lib/x86_64-linux-gnu --with-psrxml-dir=$PSRXML/install --enable-shared --enable-static --with-cfitsio F77=gfortran LDFLAGS="-L"$PSRXML"/install/lib" LIBS="-lpsrxml -lxml2" && \
    make -j $(nproc) && \
    make install
WORKDIR $HOME
RUN $PSRCHIVE/bin/psrchive_config > .psrchive.cfg && \
    sed -i 's/# ArrivalTime::default_format = Parkes/ArrivalTime::default_format = Tempo2/g' .psrchive.cfg && \
    sed -i 's/# Predictor::default = polyco/Predictor::default = tempo2/g' .psrchive.cfg && \
    sed -i 's/# Predictor::policy = ephem/Predictor::policy = default/g' .psrchive.cfg && \
    sed -i 's/# WeightedFrequency::round_to_kHz = 1/WeightedFrequency::round_to_kHz = 0/g' .psrchive.cfg

####################################### ERROR from here
# SIGPROC
ENV SIGPROC=$PSRHOME"/sigproc" \
    PATH=$PATH:$SIGPROC"/install/bin" \
    FC="gfortran" \
    F77="gfortran" \
    CC="gcc" \
    CXX="g++"
WORKDIR $SIGPROC
RUN ./bootstrap && \
    mkdir -p src && \
    cat <<EOF > src/sigproc.h
#ifdef __cplusplus
extern "C" {
#endif
#include <stdio.h>
int read_header(FILE *);
void filterbank_header(FILE *);
void dedisperse_header();
long long nsamples(char *, int, int, int, int);
int read_block(FILE *, int, float *, int);
#ifdef __cplusplus
}
#endif
EOF
RUN if [ -f src/filterbank.h ]; then \
    sed -i '1i #ifdef __cplusplus\nextern "C" {\n#endif' src/filterbank.h && \
    echo -e "#ifdef __cplusplus\n}\n#endif" >> src/filterbank.h; \
    fi
RUN ./configure --prefix=$SIGPROC/install \
                --x-libraries=/usr/lib/x86_64-linux-gnu \
                --enable-shared \
                CFLAGS="-fcommon -g -O2 -Wno-format-overflow -Wno-stringop-overflow -Wno-error" \
                FFLAGS="-fcommon -g -O2" \
                LDFLAGS="-L${TEMPO2}/lib -Wl,--allow-multiple-definition" \
                LIBS="-ltempo2 -lm" && \
    sed -i 's/malloc(strlen(bpp_search.start_time))/malloc(strlen(bpp_search.start_time) + 1)/g' src/typeof_inputdata.c || true && \
    make -j$(nproc) && \
    make install

### bash
export SIGPROC=$PSRHOME"/sigproc"
export PATH=$PATH:$SIGPROC"/install/bin"
export FC="gfortran"
export F77="gfortran"
export CC="gcc"
export CXX="g++"
cd $PSRHOME; rm -rf sigproc
git clone https://github.com/rwharton/sigproc_dsn_mod.git sigproc
cd $SIGPROC
./bootstrap

./configure --prefix=$SIGPROC/install --x-libraries=/usr/lib/x86_64-linux-gnu --enable-shared CFLAGS="-fcommon -w" LDFLAGS="-L$TEMPO2/lib" LIBS="-ltempo2"


make
make install



# sigpyproc
ENV SIGPYPROC=$PSRHOME"/sigpyproc" \
    LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$PSRHOME"/sigpyproc/lib/c"
WORKDIR $PSRHOME/sigpyproc
RUN python3 setup.py install --record list.txt --user

# DSPSR
ENV DSPSR=$PSRHOME"/dspsr" \
    PATH=$PATH:$PSRHOME"/dspsr/install/bin" \
    LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$PSRHOME"/dspsr/install/lib" \
    C_INCLUDE_PATH=$C_INCLUDE_PATH:$PSRHOME"/dspsr/install/include" \
    PKG_CONFIG_PATH=$PSRCHIVE/lib/pkgconfig:$PKG_CONFIG_PATH
WORKDIR $DSPSR
RUN echo "apsr asp bcpm bpsr caspsr cpsr cpsr2 dummy fits kat lbadr lbadr64 puma2 sigproc ska1" > backends.list && \
    ./bootstrap && \
    ./configure --prefix=$DSPSR/install --x-libraries=/usr/lib/x86_64-linux-gnu CPPFLAGS="$CPPFLAGS -I"$DAL"/install/include -I/usr/include/hdf5/serial -I/usr/local/cuda/include -I"$PSRXML"/install/include" LDFLAGS="-L"$DAL"/install/lib -L/usr/lib/x86_64-linux-gnu/hdf5/serial -L"$PSRXML"/install/lib -L/usr/local/cuda/lib64" LIBS="-lpgplot -lcpgplot -lpsrxml -lxml2" && \
    make -j $(nproc) && \
    make install

# clig
#ENV CLIG=$PSRHOME"/clig" \
#    PATH=$PATH:$PSRHOME"/clig/instal/bin" \
#    LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$PSRHOME"/clig/instal/lib"
#WORKDIR $CLIG
#RUN sed -i 's|prefix =/usr|prefix=$(CLIG)/instal|g' makefile && \
#    make && \
#    make install

# PSRSALSA
ENV PSRSALSA=$PSRHOME/psrsalsa \
    PATH=$PATH:$PSRHOME/psrsalsa/bin \
    LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$PSRHOME/psrsalsa/src/lib
WORKDIR $PSRSALSA
RUN sed -i.backup -e's/GSLFLAGS =.*/GSLFLAGS = -DGSL_VERSION_NUMBER=203/' Makefile && \
    make -j $(nproc)

# PRESTO Environment
ENV PRESTO=$PSRHOME/presto
ENV PATH=$PRESTO/bin:$PATH \
    LD_LIBRARY_PATH=$PRESTO/lib:/usr/local/lib/x86_64-linux-gnu:$LD_LIBRARY_PATH \
    PYTHONPATH=$PRESTO/python:$PYTHONPATH
# Build PRESTO C-core
WORKDIR $PRESTO/src
RUN make prep && \
    make -j$(nproc) && \
    make mpi
# Install Python dependencies and build Python wrapper
WORKDIR $PRESTO/python
RUN python3 -m pip install "numpy<2" && \
    SITE_PKGS=$(python3 -c "import site; print(site.getsitepackages()[0])") && \
    NUMPY_INCLUDE=$(python3 -c "import numpy; print(numpy.get_include())") && \
    cp -r presto $SITE_PKGS/ && \
    gcc -O3 -fPIC -shared -o $SITE_PKGS/presto/_presto.so \
        presto_src/presto_wrap.c \
        -I$PRESTO/include \
        -I/opt/miniforge3/envs/py312/include/python3.12 \
        -I$NUMPY_INCLUDE \
        -L/usr/local/lib/x86_64-linux-gnu \
        -lpresto && \
    cd /root && \
    python3 -c "from presto import sifting; print('SUCCESS: PRESTO is fully linked')"


# Install dedisp
WORKDIR $PSRHOME/dedisp
RUN sed -i "/GPU_ARCH/c\GPU_ARCH = sm_${GPU}" Makefile.inc && \
    sed -i 's/: public thrust::unary_function<unsigned int,WordType>//g' src/kernels.cuh && \
    sed -i 's/public thrust::unary_function<.*>//g' src/kernels.cuh && \
    make && \
    cp include/dedisp.h /usr/local/include/ && \
    cp lib/libdedisp.so* /usr/local/lib/ && \
    ln -snf libdedisp.so.1.0.1 /usr/local/lib/libdedisp.so.1 && \
    ln -snf libdedisp.so.1 /usr/local/lib/libdedisp.so && \
    ldconfig

# Install PSRDADA
WORKDIR $PSRHOME/psrdada
RUN ./bootstrap && \
    ./configure --prefix=$PSRHOME/psrdada && \
    make -j8 && \
    make install

# Install Heimdall
WORKDIR $PSRHOME/heimdall
RUN ./bootstrap && \
    ./configure --prefix=$PSRHOME/heimdall/linux_64 --with-psrdada-dir=$PSRHOME/psrdada --with-dedisp-dir=$PSRHOME/dedisp --with-cuda-dir=/usr/local/cuda && \
    make -j8 && \
    make install


CMD ["/bin/bash"]
