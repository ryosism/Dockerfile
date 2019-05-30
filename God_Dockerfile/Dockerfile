FROM ubuntu:14.04

ENV PATH /usr/lib/ccache:$PATH

RUN apt-get -y update && apt-get -y upgrade && \
    apt-get install -y ccache curl g++ gfortran git libhdf5-dev && \
    apt-get clean
RUN apt-get install -y python-pip python-dev && \
    apt-get clean

WORKDIR /opt/nvidia
RUN mkdir installers && \
    curl -sL -o cuda_7.0.28_linux.run http://developer.download.nvidia.com/compute/cuda/7_0/Prod/local_installers/cuda_7.0.28_linux.run && \
    echo "d1292e9c2bbaddad24c46e0b0d15a7130831bfac0382f7159321f41ae385a5ce  cuda_7.0.28_linux.run" | sha256sum -cw --quiet - && \
    chmod +x cuda_7.0.28_linux.run && sync && \
    ./cuda_7.0.28_linux.run -extract=`pwd`/installers && \
    ./installers/cuda-linux64-rel-7.0.28-19326674.run -noprompt && \
    cd / && \
    rm -rf /opt/nvidia

RUN echo "/usr/local/cuda/lib" >> /etc/ld.so.conf.d/cuda.conf && \
    echo "/usr/local/cuda/lib64" >> /etc/ld.so.conf.d/cuda.conf && \
    ldconfig

ENV CUDA_ROOT /usr/local/cuda
ENV PATH $PATH:$CUDA_ROOT/bin
ENV LD_LIBRARY_PATH $LD_LIBRARY_PATH:$CUDA_ROOT/lib64:$CUDA_ROOT/lib:/usr/local/nvidia/lib64:/usr/local/nvidia/lib
ENV LIBRARY_PATH /usr/local/nvidia/lib64:/usr/local/nvidia/lib:/usr/local/cuda/lib64/stubs$LIBRARY_PATH

ENV CUDA_VERSION 7.0
LABEL com.nvidia.volumes.needed="nvidia_driver"
LABEL com.nvidia.cuda.version="7.0"

WORKDIR /opt/cudnn
RUN curl -s -o cudnn-7.5-linux-x64-v5.0-ga.tgz http://developer.download.nvidia.com/compute/redist/cudnn/v5/cudnn-7.5-linux-x64-v5.0-ga.tgz && \
    echo "c4739a00608c3b66a004a74fc8e721848f9112c5cb15f730c1be4964b3a23b3a  cudnn-7.5-linux-x64-v5.0-ga.tgz" | sha256sum -cw --quiet - && \
    tar -xzf cudnn-7.5-linux-x64-v5.0-ga.tgz -C /usr/local && \
    rm cudnn-7.5-linux-x64-v5.0-ga.tgz

ENV CUDNN_VER v5
RUN apt-get -y -q install libopencv-dev
RUN apt-get -y -q install python-opencv
RUN ln /dev/null /dev/raw1394
RUN apt-get -y -q install vim

RUN pip install -U "setuptools"
RUN pip install -U "pip"
RUN pip install -U "cython==0.24"
RUN pip install -U "h5py"
RUN pip install -U "numpy<1.10"
RUN pip install -U "scipy"
RUN pip install -U "hacking"
RUN pip install -U "nose"
RUN pip install -U "mock"
RUN pip install -U "coverage"
RUN pip install -U "scikit-learn"

RUN git clone https://github.com/pfnet/chainer && \
    cd chainer && \
    git fetch origin pull/2213/head:nccl-multiprocess-updater && \
    git checkout nccl-multiprocess-updater && \
    python setup.py install


