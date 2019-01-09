# Using TensorFlow Serving with Docker
### Pulling a serving image
Once you have Docker installed, you can pull the latest TensorFlow Serving
docker image by running:

```shell
docker pull tensorflow/serving
```
This will pull down an minimal Docker image with TensorFlow Serving installed.
### Running a serving image
The serving images (both CPU and GPU) have the following properties:
*   Port 8500 exposed for gRPC
*   Port 8501 exposed for the REST API
*   Optional environment variable `MODEL_NAME` (defaults to `model`)
*   Optional environment variable `MODEL_BASE_PATH` (defaults to `/models`)


When the serving image runs ModelServer, it runs it as follows:

```shell
tensorflow_model_server --port=8500 --rest_api_port=8501 \
  --model_name=${MODEL_NAME} --model_base_path=${MODEL_BASE_PATH}/${MODEL_NAME}
```
To serve with Docker, you'll need:

*   An open port on your host to serve on
*   A SavedModel to serve
*   A name for your model that your client will refer to

Let's look at an example:

```shell
docker run -p 8501:8501 \
  --mount type=bind,source=/path/to/my_model/,target=/models/my_model \
  -e MODEL_NAME=my_model -t tensorflow/serving
```
In this case, we've started a Docker container, published the REST API port 8501
to our host's port 8501, and taken a model we named `my_model` and bound it to
the default model base path (`${MODEL_BASE_PATH}/${MODEL_NAME}` =
`/models/my_model`). Finally, we've filled in the environment variable
`MODEL_NAME` with `my_model`, and left `MODEL_BASE_PATH` to its default value.

This will run in the container:

```shell
tensorflow_model_server --port=8500 --rest_api_port=8501 \
  --model_name=my_model --model_base_path=/models/my_model
```

If we wanted to publish the gRPC port, we would use `-p 8500:8500`. You can have
both gRPC and REST API ports open at the same time, or choose to only open one
or the other.

### Passing additional arguments :

`tensorflow_model_server` supports many additional arguments that you could pass
to the serving docker containers. For example, if we wanted to pass a model
config file instead of specifying the model name, we could do the following:

```shell
docker run -p 8500:8500 8501:8501 \
  --mount type=bind,source=/path/to/my_model/,target=/models/my_model \
  --mount type=bind,source=/path/to/my/models.config,target=/models/models.config \
  -t tensorflow/serving --model_config_file=/models/models.config
```

This approach works for any of the other command line arguments that
`tensorflow_model_server` supports.

### Batcher

Batching of multiple requests into a single request can significantly reduce
the cost of performing inference, especially in the presence of hardware
accelerators such as GPUs. TensorFlow Serving includes a request batching
widget that lets clients easily batch their type-specific inferences across
requests into batch requests that algorithm systems can more efficiently
process.

Then for batch start the server:

```shell
docker run -p 8500:8500 \
  --mount type=bind,source=/tmp/monitored,target=/models/mnist \
  -t --entrypoint=tensorflow_model_server tensorflow/serving --enable_batching \
  --port=8500 --model_name=mnist --model_base_path=/models/mnist &
```

