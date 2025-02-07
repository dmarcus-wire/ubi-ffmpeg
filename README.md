# UBI FFMPEG

Provides Containerfiles for FFMPEG a common package for many AI/ML applications. RHEL/UBI does not provide this package and it's complicated to troubleshoot.

To build the images
```bash
# login to registry.redhat.io
podman login registry.redhat.io

# download the Containerfile locally

# build the image from the same directory you saved the Containerfile
podman build --format=docker -t ubi8-py39-ffmpeg .
```

## ubi8-ffmpeg
This Containerfile creates a container with Python 3.9, installs development tools, builds and installs FFmpeg from source, and configures the container to run with a non-root user. The result is a Python-based container with FFmpeg installed, ready to be used for media processing or other tasks involving FFmpeg.

**FROM** registry.redhat.io/ubi8/python-39:
- This sets the base image for the container. It uses Red Hat's Universal Base Image 8 (UBI 8) with Python 3.9.

**USER root:**
- This switches to the root user, allowing the installation of system dependencies and modification of system files.

**RUN yum -y install ...:**
Installs several development tools and libraries required for building FFmpeg and other software:
- gcc, gcc-c++, make, automake, autoconf, libtool, git: A set of development tools and version control software.
- It also installs the EPEL (Extra Packages for Enterprise Linux) repository by downloading and installing the epel-release package, which is needed for additional packages that aren't included by default in Red Hat-based distributions.
- Finally, yum clean all clears the cache, reducing the size of the image by removing unnecessary temporary files.

**RUN git clone --depth 1 https://git.ffmpeg.org/ffmpeg.git ffmpeg && \ ...:**
- This command clones the FFmpeg source code from its Git repository.
- It then configures the build process for FFmpeg, disabling certain optimizations (--disable-x86asm) that might not be necessary or desired for all systems.
- The make -j$(nproc) command compiles FFmpeg using the available CPU cores, speeding up the build process.
- After building FFmpeg, it installs it with make install and then deletes the cloned source code directory (rm -rf ffmpeg) to keep the image smaller.

**USER default:**
Switches back to the default user (likely a non-root user defined in the base image), which is a security best practice to avoid running applications as root.

**CMD ["/bin/bash"]:**
- Specifies the default command to run when the container starts. In this case, it runs a Bash shell. This means the container will open a shell session when executed.
