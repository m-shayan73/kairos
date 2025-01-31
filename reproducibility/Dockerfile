# Start with an official Ubuntu 22.04 base image
FROM ubuntu:22.04

# Set environment variables to prevent prompts during installations
ENV DEBIAN_FRONTEND=noninteractive

# Update and install required packages
RUN apt-get update && apt-get install -y \
    wget \
    bzip2 \
    zip \
    unzip \
    tar \
    && apt-get clean

# Download and install Anaconda 23.3.1
RUN wget https://repo.anaconda.com/archive/Anaconda3-2023.03-1-Linux-x86_64.sh -O /tmp/anaconda.sh && \
    bash /tmp/anaconda.sh -b -p /anaconda && \
    rm /tmp/anaconda.sh

# Set Anaconda path
ENV PATH="/anaconda/bin:$PATH"

# Create a Conda environment and install required libraries
RUN conda create -n kairos_env python=3.9 -y && \
    conda run -n kairos_env conda install -y psycopg2 tqdm matplotlib rice && \
    conda run -n kairos_env pip install numpy==1.22.4 scikit-learn==1.2.0 networkx==2.8.7 xxhash==3.2.0 graphviz==0.20.1 && \
    conda run -n kairos_env conda install -y pytorch==1.13.1 torchvision==0.14.1 torchaudio==0.13.1 pytorch-cuda=11.7 -c pytorch -c nvidia && \
    conda run -n kairos_env pip install torch_geometric==2.0.0 && \
    conda run -n kairos_env pip install pyg_lib torch_scatter torch_sparse torch_cluster torch_spline_conv -f https://data.pyg.org/whl/torch-1.13.0+cu117.html && \
    conda run -n kairos_env conda install -c conda-forge postgresql=15.3

# Set environment variables for the new environment
ENV CONDA_DEFAULT_ENV=kairos_env
ENV PATH="/anaconda/envs/kairos_env/bin:$PATH"

# Initialize PostgreSQL and start it
RUN conda run -n kairos_env initdb -D $CONDA_PREFIX/var/postgres && \
    conda run -n kairos_env pg_ctl -D $CONDA_PREFIX/var/postgres start

# Clean up conda caches
RUN conda clean -a

# Set the working directory inside the container
WORKDIR /workspace

# Default command
CMD ["bash"]