Bootstrap: docker
From: nvidia/cuda:9.0-cudnn7-devel-ubuntu16.04

%environment
    export PATH="/opt/conda/bin/:$PATH"
    export PATH="$PATH:/opt/julia/bin"
    export JULIA_PKGDIR="/opt/.julia"
    export PATH="$PATH:/usr/local/cuda/bin"

%appenv magma
    export MKLVARS_ARCHITECTURE=intel64
    . /opt/intel/mkl/bin/mklvars.sh

%post
    apt-get update
    apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        cmake \
        curl \
        vim \
        git \
        ca-certificates \
        libjpeg-dev \
        tmux \
        libpng-dev \

    mkdir /downloads



    #### Conda + TF + PyTorch

    curl https://repo.anaconda.com/archive/Anaconda3-5.2.0-Linux-x86_64.sh -o /downloads/conda.sh
    chmod +x /downloads/conda.sh
    /downloads/conda.sh -b -p /opt/conda
    rm /downloads/conda.sh

    export PATH="/opt/conda/bin/:$PATH"

    pip install --ignore-installed --upgrade https://storage.googleapis.com/tensorflow/linux/gpu/tensorflow_gpu-1.10.0-cp36-cp36m-linux_x86_64.whl
    conda install pytorch torchvision -c pytorch



    #### Julia

    curl https://julialang-s3.julialang.org/bin/linux/x64/1.0/julia-1.0.0-linux-x86_64.tar.gz -o /downloads/julia.tar.gz
    tar xvzf /downloads/julia.tar.gz -C /opt/
    rm -f /downloads/julia.tar.gz
    mv /opt/julia-1.0.0/ /opt/julia/



    #### Intel MKL

    curl https://apt.repos.intel.com/intel-gpg-keys/GPG-PUB-KEY-INTEL-SW-PRODUCTS-2019.PUB -o KEY.PUB
    apt-key add KEY.PUB
    rm KEY.PUB
    sh -c 'echo deb https://apt.repos.intel.com/mkl all main > /etc/apt/sources.list.d/intel-mkl.list'
    apt-get update
    apt-get install -y --no-install-recommends intel-mkl-64bit-2018.3-051

    export MKLVARS_ARCHITECTURE=intel64
    . /opt/intel/mkl/bin/mklvars.sh


    
    #### MAGMA

    apt-get install -y --no-install-recommends gfortran python2.7

    cd downloads
    curl http://icl.utk.edu/projectsfiles/magma/downloads/magma-2.4.0.tar.gz -o magma.tar.gz
    tar xvzf magma.tar.gz
    cd magma-2.4.0
    cp make.inc-examples/make.inc.mkl-gcc-ilp64 make.inc
    export CUDADIR=/usr/local/cuda
    export PATH="$PATH:/usr/local/cuda/bin"
    make -j 80
    make install prefix=/usr/local/magma

    cd /
    rm /downloads/magma.tar.gz



    #### R

    apt-get install -y --no-install-recommends libpng16-16

    apt-key adv --keyserver keyserver.ubuntu.com --recv-keys E084DAB9
    echo "deb http://cloud.r-project.org/bin/linux/ubuntu xenial/" >> /etc/apt/sources.list
    apt-get update
    apt-get install -y r-base-dev

    R --vanilla -e 'install.packages(c("tidyverse", "tikzDevice", "grid", "scales", "gtable", "gridExtra"), repos="http://cloud.r-project.org")'


    #### gnuplot
    apt-get install -y gnuplot
    #### LaTeX
    apt-get install -y texlive-full
