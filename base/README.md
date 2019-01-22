# Dockerfile for baseImage

## Content inside docker image ##

| pkg-name | version | source |
| -------- | ------- | ------ |
| CUDA     | 9.0     | nvidia/cuda|
| cuDNN    | 7.3     | tensorflow build from source dockerfile |
| Python | 3.6.5 | Anaconda installation |
| Anaconda pkgs | 3-5.2.0 | Anaconda installation |
| NCCL | 2.2 | tensorflow build from source dockerfile |

## Files in the folder ##
1.  Dockerfile
2.  jupyter_notebook_config.py
3.  run_jupyter.sh


## Dockerfile source ##

1.  CUDA installation

nivida/cuda as baseimage

2.  Anaconda installation

3.  cuDNN installation

4.  Jupyter lab ready on port 8888

5.  Docker workdir at /app

## Tag information ##

-  cuda_cudnn_ana_base:latest
-  cuda_cudnn_ana_base:9.0-7.2-5.3


## Container Registry ##


## How to use Jupyter Lab remotely ##

Login an virtual machine with GPU __in Linux CLI__:

```
sudo nvidia-docker run -it -p 8888:8888 -v <work-dir-path>:/app tftwdockerhub/cuda_cudnn_ana_base:latest
```

Template
```
sudo nvidia-docker run -it -p 8888:8888 -v <absolute workdir path on host VM>:/app <image-id/image-tag>
```

Information shall show on screen
```
[I 12:55:55.130 LabApp] Writing notebook server cookie secret to /root/.local/share/jupyter/runtime/notebook_cookie_secret
[I 12:55:55.155 LabApp] JupyterLab beta preview extension loaded from /opt/conda/lib/python3.6/site-packages/jupyterlab
[I 12:55:55.155 LabApp] JupyterLab application directory is /opt/conda/share/jupyter/lab
[I 12:55:55.160 LabApp] Serving notebooks from local directory: /app
[I 12:55:55.160 LabApp] 0 active kernels
[I 12:55:55.160 LabApp] The Jupyter Notebook is running at:
[I 12:55:55.160 LabApp] http://ac7843d7ba39:8888/?token=5c256e038136423da0d9e3fa1c400b0e86c4cf516d0836cb
[I 12:55:55.160 LabApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[C 12:55:55.160 LabApp]

    Copy/paste this URL into your browser when you connect for the first time,
    to login with a token:
http://ac7843d7ba39:8888/__?token=5c256e038136423da0d9e3fa1c400b0e86c4cf516d0836cb&token=5c256e0381xxxxxxxxxxxxxxx__

```

The __token__ is the information we need, copy and paste the token string after the following ___DNS for dsvm-gpu___

```
http://<vm-dns-address/ip-address>:8888/?token=5c256e038136423da0d9e3fa1c400b0xxxxxx
```

__copy and paste__ this link in your __local web browser__

Then you should able to see the jupyter lab which sits in a docker container that is located in an virtual machine in the cloud.

Create, change or any action performed on the host vm, use SFTP/FTP in sublime text 3 in the local PC to sync both workdir
such as download newly created item:
in sublime text 3

```
SFTP/FTP --> Sync Remote -> Local ..
```

If you see the __"success"__ key word in status bar, you shall able to check the new item locally.