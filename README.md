# idapro-docker

<p align="center"><img src="img/docker-ida.png"></p>

## About

IDA Pro (Windows) on Linux using Docker and Wine with X11 Forwarding over SSH. Why? Because I'm only paying for the Windows version but I also want to use it on Linux `¯\_(ツ)_/¯`


## build

```sh
podman build --build-arg IDA_PATH=/path/to/ida -f idapro.docker -t idapro .
```

```bash
$ ./ida.sh
Usage: ./ida.sh <ida|ida64|build|purge|run|shell|start|stop>
```

## Before you start

1. Ensure you have a SSH public key in `~/.ssh/` (`ssh-keygen` if you don't)
2. Copy your IDA Pro Windows folder into an `IDA` folder in the `idapro-docker` root folder.

Then, build the docker image with `./ida.sh build`.

## Usage

You can start a new container from the pre-built image with `./ida.sh run`

I couldn't get docker to ignore the *errors* (that can be safely ignored) thrown by `wine`. So, if first time usage, connect to the container with `./ida.sh shell` and run:

```bash
# winetricks --force corefonts
# winetricks --force dotnet452
# WINEARCH=win64 winetricks -q win10
# wine /root/python-3.10.0-amd64.exe # Add Python to PATH and install for all users
```

You can stop the container with `./ida.sh stop` and start a new container with `./ida.sh start` (without re-deploying from the docker image).

You can run IDA with `./ida.sh ida` or `./ida.sh ida64`.

- If you need a shell on the container you can use `./ida.sh shell`.
- If you need to transfer, and preserve, files between your host and the container use the local `img` mapped to `/img` in the container.
- If you want to start over, use `./ida.sh purge`.

## Remarks

- Tested with IDA Pro 8.4
- Ensure that the Python version you are running on Windows matches with the Python version that's downloaded via the `Dockerfile` and installed (manually) with `wine`. If you use `wine idapyswitch.exe`, IDA will crash every time you launch it (no idea why).

IDA should set a `regedit` `Python3TargetDLL` `key` for you. However, if when you launch IDA you receive a `WARNING` stating *Python3 is not configured (Python3TargetDLL value is not set)* run `wine regedit` and add a `Python3TargetDLL` (`REG_SZ`) value at `HKEY_CURRENT_USER\Software\Hex-Rays\IDA`. For a current **Python 3.10.x** you should set it to the value `C:\Program Files\Python310\python3.dll`. After a restart, IDA should pick up on it.

## Tips

To install Python packages use `wine pip`, as illustrated below.

```bash
$ ./ida.sh shell
# wine pip3 install z3-solver
# wine pip3 install angr
```

## References

- https://github.com/nicolaipre/idapro-docker
- https://github.com/intezer/docker-ida
- https://github.com/thawsystems/docker-ida
- https://gist.github.com/williballenthin/1c6ae0fbeabae075f1a4

