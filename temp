FROM nvcr.io/nvidia/tritonserver:21.10-py3 as full
FROM nvcr.io/nvidia/tritonserver:21.10-py3-min

COPY --from=full /opt/tritonserver/bin/tritonserver /opt/tritonserver/bin/fastdeployserver
COPY --from=full /opt/tritonserver/lib /opt/tritonserver/lib
COPY --from=full /opt/tritonserver/include /opt/tritonserver/include
COPY --from=full /opt/tritonserver/backends/python /opt/tritonserver/backends/python

COPY serving/TensorRT-8.5.2.2 /opt/TensorRT-8.5.2.2

ENV TZ=Asia/Shanghai \
    DEBIAN_FRONTEND=noninteractive \
    DCGM_VERSION=2.2.9 \

RUN apt-get update \
    && apt-key del 7fa2af80 \
    && wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/cuda-keyring_1.0-1_all.deb \
    && dpkg -i cuda-keyring_1.0-1_all.deb \
    && apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/7fa2af80.pub \
    && apt-get update && apt-get install -y --no-install-recommends datacenter-gpu-manager=1:2.2.9

RUN apt-get update \
    && apt-get install -y --no-install-recommends libre2-5 libb64-0d python3 python3-pip libarchive-dev ffmpeg libsm6 libxext6 \
    && python3 -m pip install -U pip \
    && python3 -m pip install paddlenlp fast-tokenizer-python

COPY python/dist/*.whl /opt/fastdeploy/
RUN python3 -m pip install  /opt/fastdeploy/*.whl \
    && rm -rf /opt/fastdeploy/*.whl
RUN python3 -m pip install paddlepaddle-gpu==2.4.1

COPY serving/build/libtriton_fastdeploy.so /opt/tritonserver/backends/fastdeploy/
COPY build/fastdeploy_install /opt/fastdeploy/

ENV PATH="/opt/tritonserver/bin:$PATH"
