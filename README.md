# FFmpeg with GPU acceleration

This container builds [FFmpeg](https://ffmpeg.org/) with Nvidia GPU acceleration support. [Many Nvidia GPUs](https://developer.nvidia.com/video-encode-and-decode-gpu-support-matrix-new) have hardware based [decoders](https://docs.nvidia.com/video-technologies/video-codec-sdk/nvdec-application-note/index.html) and [encoders](https://docs.nvidia.com/video-technologies/video-codec-sdk/nvenc-application-note/index.html) for commonly used video codecs. Using them can significantly accelerate encoding and decoding of videos. For more information about the Nvidia technology and hardware acceleration for FFmpeg, please see this [blog post](https://developer.nvidia.com/blog/nvidia-ffmpeg-transcoding-guide/) and the [FFmpeg hardware acceleration wiki.](https://trac.ffmpeg.org/wiki/HWAccelIntro#CUDANVENCNVDEC)

The container is based off of the [nvidia/cuda](https://hub.docker.com/r/nvidia/cuda/) base image and uses the latest commit from the [ffmpeg git repository](https://git.ffmpeg.org/gitweb/ffmpeg.git). The Dockerfile follows build instructions included in the [Video Codec SDK Documentation.](https://docs.nvidia.com/video-technologies/video-codec-sdk/ffmpeg-with-nvidia-gpu/index.html)

## Prerequisites

The container require CUDA, Docker and [Nvidia-Docker](https://github.com/NVIDIA/nvidia-docker) to be installed on the host computer. It has been tested to work on Ubuntu 21.10, CUDA 11.4 and Docker 20.10.7.

## Build

You can build the containers by checking out the source code and running:

```sh
docker build . -t nvffmpeg
```

## Usage

To invoke the ffmpeg, run:

```sh
docker run --gpus all -e VIDIA_DRIVER_CAPABILITIES=video,compute,utility  nvffmpeg
```

Note: we're passing the driver capabilities flag `-e VIDIA_DRIVER_CAPABILITIES=video,compute,utility` because by default GPU video capabilities are not exposed to the container. See [Nvidoa Docker user guide](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/user-guide.html#driver-capabilities) for more details.

```sh
docker run --gpus all -v "$PWD:$PWD" -w "$PWD" -e NVIDIA_DRIVER_CAPABILITIES=video,compute,utility  nvffmpeg -hwaccel cuda -hwaccel_output_format cuda -i input.mp4 -c:v h264_nvenc output.mp4
```

Note: we're passing `-v "$PWD:$PWD" -w "$PWD"` to docker to make the current working directory available within the running container.