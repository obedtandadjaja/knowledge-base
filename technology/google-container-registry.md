# Google Container Registry

Google's hub for Dockerfiles very similar to DockerHub.

## Add image to registry

1. Before you can push or pull images, you must configure Docker to use the gcloud command-line tool to authenticate requests to Container Registry

```
gcloud auth configure-docker
```

2. Tag the image with registry name. The tag will configure `docker push` command to push the image to the specific location.

Note that PROJECT-ID is the GCP project Id.

```
docker tag <img name> gcr.io/[PROJECT-ID]/<image name>:<tag-name - this is usually the commit sha>
```

3. Push the image to registry

```
docker push gcr.io/[PROJECT-ID]/<image name>:<tag name>
```

## Pull image from registry

```
docker pull gcr.io/[PROJECT-ID]/<image name>:<tag name>
```

## Delete image from registry

```
gcloud container images delete gcr.io/[PROJECT-ID]/<image name>:<tag name>
```
