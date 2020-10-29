`ghcr.io/openzipkin/java` is a minimal Docker image based on the OpenJDK [Alpine Linux](https://hub.docker.com/_/alpine) package.

On GitHub Container Registry: [ghcr.io/openzipkin/java](https://github.com/orgs/openzipkin/packages/container/package/java) there will be two tags
per version. The one ending in `-jre` is a minimal build including modules Zipkin related images
need. The unqualified is a JDK that also includes Maven.

## Release process

The Docker build is driven by `--build-arg java_version`. The value of this must be Alpine's
most specific Java 15 version, ex `8.252.09-r1`.
 * You can look here https://pkgs.alpinelinux.org/packages?name=openjdk8

Build the `Dockerfile` and verify the image you built matches that version.

Ex.
```bash
export DOCKER_CLI_EXPERIMENTAL=enabled
docker buildx create --name builder --use
docker buildx build --build-arg java_version=8.252.09-r1 --tag openzipkin/java:test-jre --platform=linux/amd64 --target jre --load .
```

Note: If you want to try another arch, like arm64, make sure you setup qemu first!
```bash
docker run --rm --privileged multiarch/qemu-user-static --reset -p yes
docker buildx build --build-arg java_version=8.252.09-r1 --tag openzipkin/java:test-jre --platform=linux/arm64 --target jre --load .
```

Next, verify the built image matches that `java_version`.

For example, given the following output from `docker run --rm openzipkin/java:test-jre -version`...
```
openjdk version "1.8.0_252"
OpenJDK Runtime Environment (IcedTea 3.16.0) (Alpine 8.252.09-r1)
OpenJDK 64-Bit Server VM (build 25.252-b09, mixed mode)
```
The `java_version` arg should be `8.252.09-r1`

To release the image, push a tag named the same as the `java_version` you built (ex `8.252.09-r1`).
