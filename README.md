# Transformer: PLY to LAS converter

Converts PLY files to LAS files.

### Sample Docker Command Line

Below is a sample command line that shows how the field mosaic Docker image could be run.
An explanation of the command line options used follows.
Be sure to read up on the [docker run](https://docs.docker.com/engine/reference/run/) command line for more information.

```sh
docker run --rm --mount "src=/home/test,target=/mnt,type=bind" agpipeline/ply2las:2.1 --working_space "/mnt" --metadata "/mnt/08f445ef-b8f9-421a-acf1-8b8c206c1bb8_metadata_cleaned.json" /mnt/3c807fe1-a5ba-4b4b-b618-1d2c9c981678__Top-heading-east_0.ply
```

This example command line assumes the source files are located in the `/home/test` folder of the local machine.
The name of the image to run is `agpipeline/ply2las:2.1`.

We are using the same folder for the source files and the output files.
By using multiple `--mount` options, the source and output files can be separated.

**Docker commands** \
Everything between 'docker' and the name of the image are docker commands.

- `run` indicates we want to run an image
- `--rm` automatically delete the image instance after it's run
- `--mount "src=/home/test,target=/mnt,type=bind"` mounts the `/home/test` folder to the `/mnt` folder of the running image

We mount the `/home/test` folder to the running image to make files available to the software in the image.

**Image's commands** \
The command line parameters after the image name are passed to the software inside the image.
Note that the paths provided are relative to the running image (see the --mount option specified above).

- `--working_space "/mnt"` specifies the folder to use as a workspace
- `--metadata "/mnt/08f445ef-b8f9-421a-acf1-8b8c206c1bb8_metadata.cleaned.json"` is the name of the source metadata to be cleaned
- `/mnt/3c807fe1-a5ba-4b4b-b618-1d2c9c981678__Top-heading-east_0.ply` is the name of the ply file to convert

# Original README

Below are the contents of the original README.md for the TERRA REF project.
It is included here as reference material for the history of this transformer.

# PLY to LAS conversion extractor

This extractor converts PLY 3D point cloud files into LAS files.

_Input_

  - Evaluation is triggered whenever a file is added to a dataset
  - Checks whether there are 2 east/east .PLY files
  
_Output_

  - The dataset containing the .PLY file will get a corresponding .LAS file merging the two PLY files.
  
### Docker
The Dockerfile included in this directory can be used to launch this extractor in a container.

_Building the Docker image_
```
docker build -f Dockerfile -t terra-ext-ply2las .
```

_Running the image locally_

```sh
docker run \
  -p 5672 -p 9000 --add-host="localhost:{LOCAL_IP}" \
  -e RABBITMQ_URI=amqp://{RMQ_USER}:{RMQ_PASSWORD}@localhost:5672/%2f \
  -e RABBITMQ_EXCHANGE=clowder \
  -e REGISTRATION_ENDPOINTS=http://localhost:9000/clowder/api/extractors?key={SECRET_KEY} \
  terra-ext-ply2las
```

Note that by default RabbitMQ will not allow "guest:guest" access to non-local addresses, which includes Docker. You may need to create an additional local RabbitMQ user for testing.

_Running the image remotely_

```sh
docker run \
  -e RABBITMQ_URI=amqp://{RMQ_USER}:{RMQ_PASSWORD}@rabbitmq.ncsa.illinois.edu/clowder \
  -e RABBITMQ_EXCHANGE=terra \
  -e REGISTRATION_ENDPOINTS=http://terraref.ncsa.illinosi.edu/clowder//api/extractors?key={SECRET_KEY} \
  terra-ext-ply2las
```
