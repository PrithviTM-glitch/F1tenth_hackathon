# Use the base Ubuntu image instead of an NVIDIA-specific image
FROM ubuntu:20.04

# Set non-interactive mode for apt-get
ENV DEBIAN_FRONTEND=noninteractive 

# Install prerequisites for ROS and other dependencies
RUN apt-get update && apt-get install -q -y \
    dirmngr \
    gnupg2 \
    lsb-release \
    curl \
    gnupg \
    ca-certificates \
    sudo \
    && rm -rf /var/lib/apt/lists/*

# Set up ROS repository and keys
RUN sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
RUN curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | apt-key add -

# Install ROS Noetic
RUN apt-get update && apt-get install -y ros-noetic-desktop-full && apt -y install \
    python3-rosdep \
    python3-rosinstall \
    python3-rosinstall-generator \
    python3-wstool \
    build-essential

# Initialize rosdep
RUN rosdep init && rosdep update

# Install pip and dependencies
RUN apt-get install -y python3-pip
RUN apt-get update && \
    apt-get install -y libxcb-xinerama0 libxcb-xinput0

# Install Python packages
RUN pip install --upgrade pip
RUN pip install imutils numpy scipy rdp do-mpc pathlib tensorflow==2.2

# Navigate to home directory and install gdown
WORKDIR /home
RUN pip install gdown
RUN gdown https://drive.google.com/uc?id=152KL7JzDReYdg6quBznL9WtC0I5IvNx6

# Unzip rl_library
RUN apt-get install -y unzip && unzip rl_library.zip 

# Install rl_library
WORKDIR /home/rl_library
RUN pip install -e .

# Clone the F1-10-cars repository and build it
WORKDIR /home
RUN git clone https://github.com/scope-lab-vu/F1-10-cars
WORKDIR /home/F1-10-cars
RUN git pull && apt-get update && rosdep install --from-paths src --ignore-src -r -y --rosdistro noetic
RUN /bin/bash -c "apt install -y python3-colcon-common-extensions ros-noetic-ackermann-msgs && source /opt/ros/noetic/setup.bash && colcon build"

# Optionally, if you need debugging tools
RUN apt-get install -y nano net-tools python-is-python3