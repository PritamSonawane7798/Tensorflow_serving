# Installation Guidelines
- Install TensorFlow 
```
sudo pip install tensorflow
```
- Install Keras
```
sudo pip install keras
```
- Add TensorFlow Serving distribution URI as a package source (one time setup)
```
echo "deb [arch=amd64] http://storage.googleapis.com/tensorflow-serving-apt stable tensorflow-model-server tensorflow-model-server-universal" | sudo tee /etc/apt/sources.list.d/tensorflow-serving.list && \
curl https://storage.googleapis.com/tensorflow-serving-apt/tensorflow-serving.release.pub.gpg | sudo apt-key add -

```
- Install and update TensorFlow ModelServer
```
sudo apt-get update && sudo apt-get install tensorflow-model-server
```
- apt-get upgrade tensorflow-model-server
```
sudo apt-get upgrade tensorflow-model-server
```
- Docker installation 

https://docs.docker.com/install/linux/docker-ce/ubuntu/#uninstall-old-versions


