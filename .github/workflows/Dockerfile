# Docker file for creating a container with freesurfer.
#
# Note that installs which do
# not use the linux packaging tools (to install via an .rpm or .deb file) are not officially supported.
#
# Users are expected to fill out the registration form at https://surfer.nmr.mgh.harvard.edu/registration.html
# in order to download a license.txt file received thru the email address provided in the form.  Subsequently
# you can copy the license.txt file to live under $FREESURFER_HOME as .license (see commented out COPY command below).
#
# Alternately, you can place license.txt anywhere inside the container, e.g., under $HOME, and set
# the FS_LICENSE environment variable to point to it (see commented out COPY and ENV commands below).
# Then your license file will always be found even if you install/remove other freesurfer distributions.


# Use Ubuntu 20.04 as the base image
FROM ubuntu:20.04

# Argument for the release version
ARG RELEASE_VERSION

ENV FREESURFER_HOME /usr/local/freesurfer

# shell settings - WORKDIR is set to the intended install location
WORKDIR ${FREESURFER_HOME}

# Install required packages for FreeSurfer on Ubuntu
# Using --no-install-recommends to keep the image size smaller
# DEBIAN_FRONTEND=noninteractive avoids interactive prompts during install
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
    build-essential \
    cmake \
    bc \
    libgomp1 \
    perl \
    tar \
    tcsh \
    wget \
    vim-tiny \
    libgl1-mesa-glx \
    libxext6 \
    libsm6 \
    libxrender1 \
    libxmu6 \
    libssl-dev \
    libblas-dev \
    liblapack-dev \
    libann-dev \
    libeigen3-dev \
    libopencv-dev \
    zlib1g-dev \
    libncurses-dev \
    python3 \
    python3-pip \
    python3-venv \
    dcmtk \
    xxd \
    libitk-dev \
    qt5-default \
    libqt5opengl5-dev \
    libqt5svg5-dev \
    libqt5charts5-dev \
    libqt5xml5-dev \
    libqt5network5-dev \
    libqt5gui5 \
    libqt5core5a \
    libqt5widgets5 \
    libvtk7-dev \
    && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Copy the entire checked-out repository into the image's WORKDIR
# which is already set to ${FREESURFER_HOME}.
COPY . .

# Create a build directory and run CMake
RUN mkdir build && \
    cd build && \
    cmake .. \
    -DCMAKE_INSTALL_PREFIX=${FREESURFER_HOME} \
    -DBUILD_GUIS=ON \
    -DQT_SYSLIBS=ON \
    -DVTK_SYSLIBS=ON \
    -DDISTRIBUTE_FSPYTHON=ON \
    -DINSTALL_PYTHON_DEPENDENCIES=ON \
    -DFREEVIEW_LINEPROF=OFF \
    -DBUILD_FORTRAN=ON \
    -DINFANT_MODULE=ON \
    -DCMAKE_PREFIX_PATH="/usr/lib/x86_64-linux-gnu/cmake" \
    && \
    make -j$(nproc) && \
    make install

# Clean up build directory to reduce image size
RUN rm -rf build

# setup remaining fs env variables using the already defined FREESURFER_HOME
ENV OS Linux
ENV PATH ${FREESURFER_HOME}/bin:${FREESURFER_HOME}/fsfast/bin:${FREESURFER_HOME}/tktools:${FREESURFER_HOME}/mni/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
ENV FREESURFER ${FREESURFER_HOME}
ENV SUBJECTS_DIR ${FREESURFER_HOME}/subjects
ENV LOCAL_DIR ${FREESURFER_HOME}/local
ENV FSFAST_HOME ${FREESURFER_HOME}/fsfast
ENV FMRI_ANALYSIS_DIR ${FREESURFER_HOME}/fsfast
ENV FUNCTIONALS_DIR ${FREESURFER_HOME}/sessions

# set default fs options
ENV FS_OVERRIDE 0
ENV FIX_VERTEX_AREA ""
ENV FSF_OUTPUT_FORMAT nii.gz

# mni env requirements
ENV MINC_BIN_DIR ${FREESURFER_HOME}/mni/bin
ENV MINC_LIB_DIR ${FREESURFER_HOME}/mni/lib
ENV MNI_DIR ${FREESURFER_HOME}/mni
ENV MNI_DATAPATH ${FREESURFER_HOME}/mni/data
ENV MNI_PERL5LIB ${FREESURFER_HOME}/mni/share/perl5
ENV PERL5LIB ${FREESURFER_HOME}/mni/share/perl5

# Set default command for interactive use (optional, but good practice)
CMD ["bash"]
