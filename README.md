

Bootstrap: docker
From: ubuntu:20.04

%files
    /home/pedro/Singularity_MPI/osu-micro-benchmarks-5.8.tgz /root/
    /home/pedro/Singularity_MPI/openmpi-4.0.3.tar.gz /root/

%environment
    export OSU_DIR=/usr/local/osu/libexec/osu-micro-benchmarks/mpi

%post
    apt-get -y update && DEBIAN_FRONTEND=noninteractive apt-get -y install build-essential libfabric-dev libibverbs-dev gfortran op
enssh-server
    cd /root
    tar zxvf openmpi-4.0.3.tar.gz && cd openmpi-4.0.3
    echo "Configuring and building OpenMPI..."
    ./configure --prefix=/usr && make all install clean
    export PATH=/usr/bin:$PATH
    export LD_LIBRARY_PATH=/usr/lib:$LD_LIBRARY_PATH
    cd /root
    tar zxvf osu-micro-benchmarks-5.8.tgz
    cd osu-micro-benchmarks-5.8/
    echo "Configuring and building OSU Micro-Benchmarks..."
    ./configure --prefix=/usr/local/osu CC=/usr/bin/mpicc CXX=/usr/bin/mpicxx
    make -j2 && make install

%runscript
    echo "Rank - About to run: ${OSU_DIR}/$*"
    exec ${OSU_DIR}/$*
