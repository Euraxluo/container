# 创建 python基础镜像
FROM python:3.9 as python-base
ENV TZ=Asia/Shanghai \
    PIP_NO_CACHE_DIR=off \
    PIP_DISABLE_PIP_VERSION_CHECK=on \
    PIP_DEFAULT_TIMEOUT=100 \
    POETRY_HOME="/opt/poetry" \
    POETRY_VIRTUALENVS_IN_PROJECT=true \
    POETRY_NO_INTERACTION=1 \
    PYSETUP_PATH="/opt/pysetup" \
    VENV_PATH="/opt/pysetup/.venv"

ENV PATH="$POETRY_HOME/bin:$VENV_PATH/bin:$PATH"


# 安装poetry
FROM python-base as poetry
RUN curl -sSL https://install.python-poetry.org | python - --git https://github.com/python-poetry/poetry.git@master

# 安装conda
FROM poetry as conda
ARG CONDA_VERSION=py39_4.12.0
RUN sed -e 's/deb.debian.org/mirrors.aliyun.com/g' -i  /etc/apt/sources.list &&  \
    sed -e 's/security.debian.org/mirrors.aliyun.com/g' -i  /etc/apt/sources.list && \
    apt-get update -q &&  apt-get install -q -y --no-install-recommends  \
    vim  curl wget  git  net-tools  telnet  bzip2 ca-certificates git libglib2.0-0 libsm6 libxext6 libxrender1 mercurial openssh-client procps subversion &&  \
    apt-get clean && rm -rf /var/lib/apt/lists/*

ENV PATH="$/opt/conda/bin:$PATH"

RUN /bin/sh -c set -x && UNAME_M="$(uname -m)" && if [ "${UNAME_M}" = "x86_64" ]; then MINICONDA_URL="https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh"; SHA256SUM="78f39f9bae971ec1ae7969f0516017f2413f17796670f7040725dd83fcff5689"; elif [ "${UNAME_M}" = "s390x" ]; then MINICONDA_URL="https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-s390x.sh"; SHA256SUM="ff6fdad3068ab5b15939c6f422ac329fa005d56ee0876c985e22e622d930e424"; elif [ "${UNAME_M}" = "aarch64" ]; then MINICONDA_URL="https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-aarch64.sh"; SHA256SUM="5f4f865812101fdc747cea5b820806f678bb50fe0a61f19dc8aa369c52c4e513"; elif [ "${UNAME_M}" = "ppc64le" ]; then MINICONDA_URL="https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-ppc64le.sh"; SHA256SUM="1fe3305d0ccc9e55b336b051ae12d82f33af408af4b560625674fa7ad915102b"; fi && wget "${MINICONDA_URL}" -O miniconda.sh -q && echo "${SHA256SUM} miniconda.sh" > shasum && if [ "${CONDA_VERSION}" != "latest" ]; then sha256sum --check --status shasum; fi && mkdir -p /opt && sh miniconda.sh -b -p /opt/conda && rm miniconda.sh shasum && ln -s /opt/conda/etc/profile.d/conda.sh /etc/profile.d/conda.sh && echo ". /opt/conda/etc/profile.d/conda.sh" >> ~/.bashrc && echo "conda activate base" >> ~/.bashrc && find /opt/conda/ -follow -type f -name '*.a' -delete && find /opt/conda/ -follow -type f -name '*.js.map' -delete && /opt/conda/bin/conda clean -afy

