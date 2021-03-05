# Bazel rules for ROS

This repo aims to build ROS (1) from scratch.

## Prerequisites

The code is developed and tested on Ubuntu 20.04 with Python 3.8.

You will need to install Bazel, see [here](https://docs.bazel.build/versions/master/install.html).

Besides Bazel, you will need some additional deps:

```sh
sudo apt install libbz2-dev liblz4-dev
```

plus a C++ compiler and a Python 3.8 interpreter. At least on Ubuntu 20.04,
you'll need to also do the following:

```sh
sudo ln -s /usr/bin/python3 /usr/bin/python
```

Bazel-generated driver scripts for Python binaries still use `/usr/bin/python`.

And no, you don't have to install any ROS packages via `apt`.

## What works?

So far a subset of ros-core packages can be built.

Here is an example.

Let's begin with starting `roscore`:

```sh
bazel run //:roscore
```

In a separate terminal, let's start a (C++) talker node:

```sh
bazel run //examples:talker
```

This single command will compile and run the talker node.

In a yet another terminal we can start a listener node:

```sh
bazel run //example:listener  # C++ version or
bazel run //example:py_listener  # Python version
```

Rosbag recording & playing works as well:

```sh
bazel run //:rosbag_record -- /chatter -o /tmp/foo.bag  # to record a bag or
bazel run //:rosbag_play -- /tmp/foo_<timestamp>.bag  # to play a bag
```

`rostopic`, tied to this example (see `examples/BUILD.bazel` for more info) can
be used as

```sh
bazel run //examples:rostopic -- echo /chatter
```

Not too shabby.

Now let's start a deployment with the talker and the listener nodes:
```sh
bazel run //example:chatter

```
This command will build the necessary nodes and launch them. This is similar
to executing good-ol' `roslaunch`, but, running the chatter `ros_launch` target
using Bazel ensures all necessary dependencies are (re-)built.

How about executing the chatter deployment within a Docker container?
Just run
```sh
bazel run //example:chatter_image
```
FYI, the size of a compressed image made with `--config=opt` is less than
50MB -- check [here](https://hub.docker.com/repository/docker/mvukov/chatter).
A very simple base image can be found in `docker/base`.


## Additional

Optionally you can install `poetry` for resolving/updating Python deps:

```sh
sudo python3.8 -m pip install poetry
```

Then you can run the script `./generate_python_requirements.sh` to update
Python deps.
