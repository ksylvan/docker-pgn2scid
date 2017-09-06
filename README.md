# docker-pgn2scid

pgn2scid in a container

To run this image, share the X11 socket or use any
of the other methods to run X11 Apps in Docker.

For example, you can run the image like this on Linux. With this snippet
in your `~/.bahsrc`:

```
pgn2scid()
{
  docker run -it -u 1000:1000 --rm -e HOME=/home/scid \
    -e DISPLAY=unix:0 -e XAUTHORITY=/tmp/xauth \
    -v $XAUTHORITY:/tmp/xauth -v $HOME:/home/scid \
    -v /etc/passwd:/etc/passwd:ro -v /etc/group:/etc/group:ro \
    -v /tmp/.X11-unix:/tmp/.X11-unix \
    ${1+"$@"} kayvan/pgn2scid
}
```

Run it by `pgn2scid`.

## MacOS: Using this image

On MacOS, if you wish to run this image, you need to install `XQuartz` and
`socat`. With `brew` installed, do this:

```
brew cask install xquartz
brew install socat
```

Then you can place this bash snippet in your `~/.bash_profile`:

```sh
__my_ip=$(ifconfig|grep 'inet '|grep -v '127.0.0.1'| \
            head -1|awk '{print $2}')
pgn2scid() {
  killall -0 quartz-wm > /dev/null 2>&1
  if [ $? -ne 0 ]; then
    echo "ERROR: Quartz is not running. Start Quartz and try again."
  else
    socat TCP-LISTEN:6001,reuseaddr,fork UNIX-CLIENT:\"$DISPLAY\" &
    SOCAT_PGN_SCID_PID=$!
    docker run --rm \
      -e HOME=/home/scid \
      -e XAUTHORITY=/tmp/xauth -v ~/.Xauthority:/tmp/xauth \
      -e DISPLAY=$__my_ip:1 --net host -v $HOME:/home/scid \
      ${1+"$@"} kayvan/pgn2scid
    kill $SOCAT_PGN_SCID_PID
  fi
}
```

Now, `pgn2scid` should launch the application.

# Reference

- https://github.com/CasualPyDev/pgn2scid
