# geekOS + JetBrains CLion

This README, along with some assorted files in this repository, includes some of
the modifications to the standard UMD CMSC412 (Operating Systems) geekOS
distribution from spring 2021. It does not include any code used to implement
projects from the course.

## Why?

Using a fully fledged IDE has many advantages over editors like VIM/Emacs or
even Visual Studio Code. While tools like VIM/Emacs eschews fixed functionality
and integrations in favor of extensive customizability, their learning curve
means those without substantial experience may have trouble tweaking them to
satisfaction in the timespan of a single semester of intensive operating system
development. Visual Studio Code strike a more middle-ground balance, still
featuring customizability, while also being friendlier to new users by providing
an extensive plugin/add-on environment and a GUI for configuring everything to
users' tastes.

I chose to use CLion for a couple of reasons:

1. I'm already familiar with JetBrains IDEs, their functionality, and making my
   way around their user interfaces.
2. I'm a big fan of their GUI debuggers, which I figured would find frequent use
   in an operating systems course.
3. I didn't want to learn my way around Visual Studio Code for a single class,
   especially because it seems the amount of setup required to have proper
   syntax highlighting/autocompletion for VS Code was similar to CLion.

## Disclaimer

The standard workflow for CMSC412 recommends the usage of Docker/Vagrant as it
means that students and the submit server will be running geekOS in identical
environments. In order to setup CLion with the provided Docker configuration,
you _must_ modify the container which means you are no longer using the course
Docker container.

While care has been taken to ensure the accuracy of the content of this
respository, instructions, code, and any other resources in this repository are
provided AS-IS without warranty of any kind, expresseed or implied, including
but not limited to the warranties of merchantability, fitness for a particular
purpose and noninfringement. In no event shall the authors or copyright holders
be liable for for any claim, damages or other liability, whether in an action of
contract, tort or otherwise, arise from, out of or in connection with the
software or the use of other dealings in the software.

AKA if it turns out one of these modifications means compilation on your machine
but not on the submit server, too bad for you!

## Compatibility

These resources were created in spring 2021 for use with Windows 10 20H2 with
WSL2 running Ubuntu 20.04 and CLion 2020.3. I have also done limited testing on
native Ubuntu 20.10 with CLion 2020.3 (this was my backup configuration should
Windows fail).

In mid spring 2021, Jetbrains released CLion 2021.1 which included support for
remote makefile projects. However, the debugger fails to start. Version 2020.3
is recommended.

## Setup

On Windows, geekOS development can either be performed via Docker using the WSL2
backend or Docker on a Linux virtual machine. Vagrant is another option, but
I've personally never used it so this guide omits any suggestions on how to set
it up.

The crux of setting up CLion to work with the Docker and geekOS environment is
the setup of the CLion debugger. In the standard suggested workflow, a gdb
server is started in the form of running geekOS through QEMU. A separate shell
must connect to the gdb server.

## Instructions 

Performing the following sections in the listed order is recommended, but 
possibly not required under certain circumstances.

### Opening the project

Do not import the project; open the folder containing the project. TODO: project
root and vcs root may not match and may need to be updated after opening the 
project

### Dockerfile

In the Dockerfile, make the following modifications:

1. At the beginning of the file, change `ubuntu:trusty` to `ubuntu:xenial`. The
   Canonical package repository versions of gdb, gcc, etc. are too old for
   CLion. The easy solution is to just use a more recent distribution of Ubuntu.
2. (Optional?) Add the following snippet at the end of the Dockerfile:

```shell
# clion config
RUN apt-get -y install openssh-server
RUN apt-get -y install cmake

RUN ( \
    echo 'LogLevel DEBUG2'; \
    echo 'PermitRootLogin yes'; \
    echo 'PasswordAuthentication yes'; \
    echo 'Subsystem sftp /usr/lib/openssh/sftp-server'; \
    echo 'PermitEmptyPasswords yes'; \
  ) > /etc/ssh/sshd_config_test_clion \
  && mkdir /run/sshd

RUN useradd -m clion \
    && yes password | passwd clion

CMD ["/usr/sbin/sshd", "-D", "-e", "-f", "/etc/ssh/sshd_config_test_clion"]
```

3. (Optional) After `ruby`, add `default-jre`. The submit server script requires
   Java. By default, the distributed Dockerfile doesn't include Java so if 
   you want to submit the project, you'd have to exit the container or use a 
   different terminal window. If you want to be lazy, just add `default-jre` 
   and you'll be able to `make submit` or `java -jar submit.jar` from within 
   the Docker container.

### Makefile

The changes you make to the Dockerfile will create incompatibilities as changes
to gcc in more recent versions will prevent the project from compiling. Perform
the following changes:

1. TODO

### CLion configuration

TODO: Setup remote gcc/make/etc.
TODO: Setup remote debugging

### Debugging

TODO: what to do when it doesn't work
