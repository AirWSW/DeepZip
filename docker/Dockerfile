ARG cuda_version=9.0
ARG cudnn_version=7
FROM nvidia/cuda:${cuda_version}-cudnn${cudnn_version}-devel

# Use mirrors.xjtu.edu.cn
RUN sed -i "s/archive.ubuntu.com/mirrors.xjtu.edu.cn/g" /etc/apt/sources.list

# Install system packages
RUN apt-get update && apt-get install -y --no-install-recommends \
      bzip2 \
      g++ \
      git \
      graphviz \
      libgl1-mesa-glx \
      libhdf5-dev \
      openmpi-bin \
      time \
      wget \
      sudo && \
    rm -rf /var/lib/apt/lists/*

# Install conda
ENV CONDA_DIR /opt/conda
ENV PATH $CONDA_DIR/bin:$PATH

# Use mirrors.xjtu.edu.cn
RUN wget --quiet --no-check-certificate https://mirrors.xjtu.edu.cn/anaconda/miniconda/Miniconda3-4.2.12-Linux-x86_64.sh && \
    echo "c59b3dd3cad550ac7596e0d599b91e75d88826db132e4146030ef471bb434e9a *Miniconda3-4.2.12-Linux-x86_64.sh" | sha256sum -c - && \
    /bin/bash /Miniconda3-4.2.12-Linux-x86_64.sh -f -b -p $CONDA_DIR && \
    rm Miniconda3-4.2.12-Linux-x86_64.sh && \
    echo export PATH=$CONDA_DIR/bin:'$PATH' > /etc/profile.d/conda.sh

# Install Python packages and keras
ARG nb_user=keras
ARG nb_uid=1000

RUN useradd -m -s /bin/bash -u $nb_uid $nb_user && \
    chown $nb_user:$nb_user $CONDA_DIR -R && \
    mkdir -p /src && \
    chown $nb_user:$nb_user /src

# Add user to nopasswd sudoers
RUN echo "$nb_user ALL=(ALL:ALL) NOPASSWD:ALL" >> /etc/sudoers

USER $nb_user

ARG python_version=3.6

# Use mirrors.xjtu.edu.cn
RUN conda config --add channels https://mirrors.xjtu.edu.cn/anaconda/pkgs/main/ && \
    conda config --add channels https://mirrors.xjtu.edu.cn/anaconda/pkgs/free/ && \
    conda config --set show_channel_urls yes

# Use mirrors.bfsu.edu.cn
RUN conda install -y python=${python_version} && \
    pip install --upgrade pip && \
    pip config set global.index-url https://mirrors.bfsu.edu.cn/pypi/web/simple && \
    pip install \
      sklearn_pandas \
      tensorflow-gpu==1.8.0 \
      keras==2.2.2 \
      numpy==1.16.4 && \
    conda install \
      bcolz \
      h5py \
      matplotlib \
      mkl \
      nose \
      notebook \
      Pillow \
      pandas \
      pydot \
      pygpu \
      pyyaml \
      scikit-learn \
      six && \
    conda clean -yt

ENV PYTHONPATH='/src/:$PYTHONPATH'

WORKDIR /src

EXPOSE 8888

CMD jupyter notebook --port=8888 --ip=0.0.0.0

