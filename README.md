# Docker Pupy

Pupy is a cross-platform, multi function RAT and post-exploitation tool mainly written in python. This is a dockerized version of Pupy.

## Getting Started

Pull Image (e.g Image created at f8c829dd66449888ec3f4c7d086e607060bca892 Pupy commit)

```
docker image pull cyb3rward0g/docker-pupy:f8c829dd66449888ec3f4c7d086e607060bca892
```

Tag image

```
docker tag cyb3rward0g/docker-pupy:f8c829dd66449888ec3f4c7d086e607060bca892 docker-pupy
```

Run Image

```
docker run --rm -it -p 1234:1234 docker-pupy python pupysh.py
```

Maybe you want to mount a host folder where you have all your payloads

```
docker run --rm -it -p 1234:1234 -v "/opt/payloads:/tmp/payloads" docker-pupy python pupysh.py
```
