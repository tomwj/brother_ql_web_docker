
## brother\_ql\_web Dockerfile

This repository contains the Dockerfile to create images for the application
[brother\_ql\_web](https://github.com/pklaus/brother_ql_web), a web interface
to print labels with Brother's QL-series label printers.

*Ready to use images* can be found on Docker hub:
[pklaus/brother\_ql\_web](https://hub.docker.com/r/pklaus/brother_ql_web).

### Base Image / Tags

The image created are based on alpine and built for multiple
platforms/architectures. Your docker daemon will automatically
pick a suitable version when pulling the image.
Running this on a Raspberry Pi works like a charm!

The `:latest` tag is the most up-to-date version of the code.
Furthermore, there are tags like `:alpine_1234567` available,
they contain a specific commit of brother\_ql\_web if you want
to pin your deployment to a specific version.

### Usage

An example running the application with a QL-700 connected **via USB**:

Create a `docker-compose.yaml` file containing
```
version: '3'

services:
  print:
    image: pklaus/brother_ql_web
    command: ./brother_ql_web.py --model QL-700 file:///dev/usb/lp0
    restart: unless-stopped
    ports:
      - 8013:8013
    devices:
      - /dev/usb/lp0:/dev/usb/lp0
    #Uncomment below, to use system fonts
    #volumes:
    #  - /usr/share/fonts:/usr/share/fonts
    # Or for user fonts
    #  - ~/.fonts:/usr/share/fonts
```
Run, this command in the folder where you created the `docker-compose.yaml`
```
docker-compose up --detach
```
If you make changes to `docker-compose.yaml` you can run `docker-compose up --detach` again. and to see logs run `docker-compose logs`

Then you can access the web interface over via http://your-docker-host-ip:8013.

Instead of `--device=/dev/usb/lp0` you might also consider
`--volume /dev/usb/:/dev/usb/` which should be more robust
against device disconnects / reconnects.

If you have a **networked printer** like the QL-820NWB, you could start the GUI like this:

```
docker run \
  --name brother_ql_web \
  --detach \
  --rm \
  --publish 8013:8013 \
  --restart=always \
  pklaus/brother_ql_web:latest \
  ./brother_ql_web.py --model QL-820NWB tcp://192.168.0.57
```

For more options, consult the `--help`:

```
docker run --rm \
  pklaus/brother_ql_web:latest \
  ./brother_ql_web.py --help
```

To **stop/kill and remove the container**, run:

`docker kill brother_ql_web`
