# OpenRoSDE Platform Sciurus17

The RT Sciurus17 platform of [OpenRoSDE](https://github.com/openrosde/openrosde), read "Open Robotic SDE", the open-source Software Development Environment for collaborative robotics research maintained by [Coarobo GK](https://coarobo.com/).
It is an extension that OpenRoSDE finds by name once it is cloned into `extensions/openrosde-platform-sciurus17/` of the core.

[![Test with OpenRoSDE](https://github.com/openrosde/openrosde-platform-sciurus17/actions/workflows/test-with-openrosde.yml/badge.svg?branch=main)](https://github.com/openrosde/openrosde-platform-sciurus17/actions/workflows/test-with-openrosde.yml)
[![ROS Noetic](https://img.shields.io/badge/ROS_Noetic-Framework-blue?logo=ros&logoColor=white)](http://wiki.ros.org/noetic)
[![Gazebo](https://img.shields.io/badge/Gazebo-Simulator-blue)](http://gazebosim.org/)
[![OpenRoSDE](https://img.shields.io/badge/OpenRoSDE-Platform-blue)](https://github.com/openrosde/openrosde)

[![License: Apache 2.0](https://img.shields.io/badge/License-Apache_2.0-green)](LICENSE)

## 🤖 Robot Platform

| Platform    | Robot                                                 | ROS          | Documentation                                                          |
| ----------- | ----------------------------------------------------- | ------------ | ---------------------------------------------------------------------- |
| `sciurus17` | [RT Sciurus17](https://rt-net.jp/products/sciurus17/) | ROS 1 Noetic | [docs/robot-platforms/sciurus17.md](docs/robot-platforms/sciurus17.md) |

## 🚀 Quick Start

1. Clone and set up the core of OpenRoSDE on the Ubuntu development machine, as its [Quick Start](https://github.com/openrosde/openrosde#-quick-start) describes:

    ```shell
    git clone https://github.com/openrosde/openrosde.git && cd openrosde/
    bash ./SETUP-DEVEL-MACHINE.bash
    ```

2. Clone this platform into `extensions/` of the core:

    ```shell
    git clone https://github.com/openrosde/openrosde-platform-sciurus17.git extensions/openrosde-platform-sciurus17
    ```

3. Switch to the platform, which also clones the Git submodules of every extension and applies their patches:

    ```shell
    bash ./SWITCH-ROBOT-PLATFORM.bash sciurus17
    ```

4. Pull or build the Docker images, then run the container and build the Catkin workspaces:

    ```shell
    bash ./BUILD-DOCKER-IMAGE.bash
    bash ./RUN-DOCKER-CONTAINER.bash
    openrosde-initialize-new-container
    ```

5. Exit the container (`Ctrl+d`) and open the `tmux` session of the platform:

    ```shell
    bash ./RUN-TMUX-SESSION.bash
    ```

The core needs no change to find the platform, since `bash ./SWITCH-ROBOT-PLATFORM.bash` lists every platform of the cloned extensions.
Update the platform with `git -C extensions/openrosde-platform-sciurus17 pull`, then `bash ./scripts/update-git-submodules.bash`.

## 📁 Directory Structure

| Path                                        | Content                                                                                                                                                  |
| ------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `env/sciurus17.env`                         | Default setup of the platform, which `bash ./SWITCH-ROBOT-PLATFORM.bash sciurus17` copies to `.env.custom` of the core.                                  |
| `docker/sciurus17-devel/Dockerfile`         | Platform image `sciurus17-devel`, built on the image `openrosde-base` of the core.                                                                       |
| `docker/docker-compose-sciurus17.yaml`      | Docker Compose overlay of the platform.                                                                                                                  |
| `ros/sciurus17_catkin_ws/`                  | Catkin workspace with `openrosde_sciurus17_launch`, `openrosde_sciurus17_behaviors`, and the Git submodules `sciurus17_ros` and `sciurus17_description`. |
| `tmux/sciurus17.panes`                      | Panes of the `tmux` sessions of the platform.                                                                                                            |
| `terminator/sciurus17.config`               | Terminator layouts of the platform, until `tmux` replaces them.                                                                                          |
| `scripts/sciurus17/`                        | Bringup of the robot over SSH and restart of Chrony on the robot.                                                                                        |
| `docs/robot-platforms/sciurus17.md`         | Documentation of the platform.                                                                                                                           |
| `.github/workflows/test-with-openrosde.yml` | Workflow that tests this platform with the core of OpenRoSDE, as [Continuous Integration](#-continuous-integration) describes.                           |
| `.gitmodules`                               | Git submodules of the Catkin workspace, which `bash ./scripts/update-git-submodules.bash` of the core clones.                                            |
| `LICENSE`                                   | Text of the Apache License 2.0.                                                                                                                          |
| `NOTICE`                                    | MIT notice of the earlier code and notices of third-party software.                                                                                      |

The layout is the one of the core, which the [Extensions](https://github.com/openrosde/openrosde/blob/dev/docs/extensions.md) guide of OpenRoSDE describes.

## 📖 Documentation

| Document                                                                                               | Description                                                      |
| ------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------- |
| [Robot Platforms / Sciurus17](docs/robot-platforms/sciurus17.md)                                       | RT Sciurus17 configuration, simulation, and operation            |
| [`openrosde_sciurus17_launch`](ros/sciurus17_catkin_ws/src/openrosde_sciurus17_launch/README.md)       | Launch files of the platform                                     |
| [`openrosde_sciurus17_behaviors`](ros/sciurus17_catkin_ws/src/openrosde_sciurus17_behaviors/README.md) | FlexBE behaviors of the platform                                 |
| [Basic Usage](https://github.com/openrosde/openrosde/blob/dev/docs/basic-usage.md)                     | Full setup guide of the core of OpenRoSDE                        |
| [Extensions](https://github.com/openrosde/openrosde/blob/dev/docs/extensions.md)                       | How OpenRoSDE finds the files of a cloned extension and tests it |

## 🧪 Continuous Integration

`.github/workflows/test-with-openrosde.yml` runs on every push to `main`, on every pull request, and by hand (`workflow_dispatch`).
It calls the reusable workflow `extension.yml` of the core, `openrosde/openrosde/.github/workflows/extension.yml@dev` with `core-ref: dev`, which publishes nothing:

- It checks out the core on `dev` and clones the public extensions that `gitlab-ci/extensions.repos` of the core pins.
- It checks out the commit under test in place of the pinned clone of this platform, so that a pull request from a fork is tested too.
- It tests only the setups that activate this platform, and builds the image of the platform locally on the published images of the core.
- It builds the Catkin workspaces in that image, then checks every launch entry point of the panes in a container with no network.
- It also checks that no script of the CI can move a robot, and that the meshes of the workspaces convert into valid GLB files.

The CI of the core of OpenRoSDE tests this platform at the commit that `gitlab-ci/extensions.repos` of the core pins: it builds or reuses the image `ghcr.io/openrosde/sciurus17-devel`, builds the Catkin workspaces in it, and checks every launch entry point of the panes.
It publishes the image as `dev` or `main` from those branches of the core, and under the tag of a release of the core, such as `v3.0.0`.

This platform holds no `ci/check-in-container.bash` of its own.

## 🔗 External Resources

- OpenRoSDE: [https://github.com/openrosde/openrosde](https://github.com/openrosde/openrosde)
- OpenRoSDE website: [https://openrosde.org/](https://openrosde.org/)
- Coarobo GK: [https://coarobo.com/](https://coarobo.com/)
- RT Sciurus17: [https://rt-net.jp/products/sciurus17/](https://rt-net.jp/products/sciurus17/)

## 🐛 Reporting Issues

Please report any issue of this platform using the GitHub issue tracker: [https://github.com/openrosde/openrosde-platform-sciurus17/issues](https://github.com/openrosde/openrosde-platform-sciurus17/issues).
Report any issue of the scripts, the images, or the CI of OpenRoSDE in the issue tracker of the core: [https://github.com/openrosde/openrosde/issues](https://github.com/openrosde/openrosde/issues).

## 🤝 Contributing

- This repository uses explicit commit messages following the [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/) specification.
- Open pull requests into `main`, where the workflow of [Continuous Integration](#-continuous-integration) tests them with the core.
- This repository follows the conventions of the core, which [https://github.com/openrosde/openrosde/blob/dev/AGENTS.md](https://github.com/openrosde/openrosde/blob/dev/AGENTS.md) and [https://github.com/openrosde/openrosde/blob/dev/CONTRIBUTING.md](https://github.com/openrosde/openrosde/blob/dev/CONTRIBUTING.md) describe.
- The contributing guide of the organization applies to this repository, which has none of its own: [https://github.com/openrosde/.github/blob/main/CONTRIBUTING.md](https://github.com/openrosde/.github/blob/main/CONTRIBUTING.md).
- Report a security vulnerability privately, as the security policy of the organization describes: [https://github.com/openrosde/.github/blob/main/SECURITY.md](https://github.com/openrosde/.github/blob/main/SECURITY.md).

## 👥 Authors & Acknowledgment

OpenRoSDE is maintained by [Coarobo GK](https://coarobo.com/).
Its maintainers, authors, and contributors are listed in the README of the core: [https://github.com/openrosde/openrosde#-authors--acknowledgment](https://github.com/openrosde/openrosde#-authors--acknowledgment).

## 🕰️ History

The files of this platform come from the SDE, the predecessor of OpenRoSDE archived at [https://gitlab.com/coarobo/SDE](https://gitlab.com/coarobo/SDE), at commit [`041d164`](https://gitlab.com/coarobo/SDE/-/commit/041d164201db41ac35db5efd7889e6424a84deca) of `dev`, where their earlier history lives.

## ⚖️ License

```
Copyright 2019 Coarobo GK

Licensed under the Apache License, Version 2.0
```

See [`LICENSE`](LICENSE) for the license, and [`NOTICE`](NOTICE) for the MIT notice of the earlier code and the notices of third-party software.

## 📜 Third-Party Software

This repository holds no third-party code apart from the Git submodules that it pins, which it does not redistribute.
The image `sciurus17-devel` and the Catkin workspace bring the third-party software below, each under its own license, as [`NOTICE`](NOTICE) records.

| Component                                                                        | Source                                                                                               | License                                                                                                                          |
| -------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| `sciurus17_ros`                                                                  | Git submodule from https://github.com/rt-net/sciurus17_ros, branch `master`, commit `2a3cdeb`        | [Apache-2.0](https://spdx.org/licenses/Apache-2.0.html) (RT Corporation)                                                         |
| `sciurus17_description`                                                          | Git submodule from https://github.com/rt-net/sciurus17_description, branch `main`, commit `21a7720`  | Non-commercial license of RT Corporation (非商用使用許諾規約), https://github.com/rt-net/sciurus17_description/blob/main/LICENSE |
| Intel RealSense packages `realsense2_camera` and `realsense2_description`        | Debian packages of the image `sciurus17-devel`, from https://github.com/IntelRealSense/realsense-ros | [Apache-2.0](https://spdx.org/licenses/Apache-2.0.html) (Intel Corporation)                                                      |
| ROS Noetic, Gazebo, FlexBE, and the other packages of the image `openrosde-base` | The core of OpenRoSDE, https://github.com/openrosde/openrosde                                        | Per upstream, such as BSD, Apache-2.0, or MIT                                                                                    |

> [!WARNING]
> `sciurus17_description`, which holds the description and the meshes of the robot, is licensed under the non-commercial license of RT Corporation, which allows research and education only.
> Any commercial use needs a prior agreement with RT Corporation, as its `LICENSE` says.
> This repository only pins it as a Git submodule, which `scripts/update-git-submodules.bash` of the core clones from RT Corporation, and never redistributes it.
