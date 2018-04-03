FROM ubuntu:16.04

# nGraph dependencies
RUN apt-get update && apt-get install -y \
  build-essential \
  cmake \
  clang-3.9 \
  clang-format-3.9 \
  git \
  curl \
  zlib1g \
  zlib1g-dev \
  libtinfo-dev && \
  apt-get clean autoclean && apt-get autoremove -y

# Python dependencies
RUN apt-get update && apt-get install -y \
  python \
  python3 \
  python-dev \
  python3-dev \
  python-pip && \
  apt-get clean autoclean && apt-get autoremove -y
RUN pip install --upgrade pip setuptools wheel
RUN pip install tox

# Test nGraph
#COPY . /root/ngraph
WORKDIR /root
RUN git clone https://github.com/NervanaSystems/ngraph.git

RUN mkdir /root/ngraph/build
WORKDIR /root/ngraph/build
RUN cmake ../ -DNGRAPH_USE_PREBUILT_LLVM=TRUE
RUN make -j 8
RUN make check
RUN make install

# Test nGraph Python API
RUN mkdir /root/ngraph/python/build
WORKDIR /root/ngraph/python/build
RUN cmake -DNGRAPH_INSTALL_PREFIX=/root/ngraph_dist ..
RUN make -j 8
ENV NGRAPH_CPP_BUILD_PATH /root/ngraph_dist
ENV LD_LIBRARY_PATH /root/ngraph_dist/lib
ENV PYBIND_HEADERS_PATH /root/ngraph/python/build/pybind11
CMD cd /root/ngraph/build && make check ; cd /root/ngraph/python && tox