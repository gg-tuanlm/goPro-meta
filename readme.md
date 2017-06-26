# App to extract images asociated with GPS

## Purpose:
  
Given a GoPro movie with metadata (gpmf format), the application extracts images
with the given framerate and interpolates the GPS coordinates, speed, 
accelerometer, gyro, and any other desired metadata.

## Dependencies

- Boost (for filesystem stuff):

```sh
  $ sudo apt install libboost-all-dev
```
- Opencv3: [Link](http://docs.opencv.org/3.0-beta/doc/tutorials/introduction/linux_install/linux_install.html)

- libyaml-cpp for yaml file creation and parsing: [Link](https://github.com/jbeder/yaml-cpp):

  - Ubuntu 16.04 (apt-get installs v0.5.2):

    ```sh
      $ sudo apt-get install libyaml-cpp-dev
    ```
  - Ubuntu 14.04 (apt-get installs v0.5.1 which does not contain yaml-cpp-config.cmake):

    ```sh
      $ git clone https://github.com/jbeder/yaml-cpp.git
      $ cd yaml-cpp/
      $ mkdir build
      $ cd build/
      $ cmake ..
      $ make -j 
      $ sudo make install
    ```
## Usage

This code uses the gpmf-parser so that submodule has to be cloned into its
folder (../gpmf-parser):

```sh
  $ git submodule init
  $ git submodule update
```

After that we can compile and run the script, which parses the metadata in
video.mp4 at a frame rate of 3fps, and it saves images and yaml file to folder
/tmp/output:

```sh
  $ cd img_gps_extractor
  $ mkdir build && cd build
  $ cmake ..
  $ make -j
  $ ./img_gps_extractor -i video.mp4 -f 3 -o /tmp/output
```


## Format of the output .yaml file:

This is temporary, but it gives an idea of how the final one will look like:

- ts is timestamp in seconds
- gps is:
  - lat (degrees)
  - long (degrees)
  - altitude (meters)
  - 2D earth speed magnitude (m/s)
  - 3D speed magnitude (m/s)

```yaml
000000.jpg:
  ts: 0
  gps:
    lat: 33.1265
    long: -117.3274
    alt: -20.184
    2dv: 0.167
    3dv: 0.19
000001.jpg:
  ts: 1.001
  gps:
    lat: 33.1265
    long: -117.3274
    alt: -20.05719
    2dv: 0.730125
    3dv: 0.689375
```

To parse the yaml file from python:

```python
import yaml
with open("metadata.yaml", 'r') as stream:
  data= yaml.load(stream)
data['000001.jpg']['gps']['lat'] #latitude gps of image 000001.jpg
data['000034.jpg']['ts'] #timestamp for image 000034.jpg
```
# Comments and credits

- Only gps extraction is implemented so far. More metadata extraction 
will be added.

- Libraries used:
  - [Boost](https://github.com/boostorg/boost) ([@boostorg](https://github.com/boostorg))
  - [GoPro GPMF Parser](https://github.com/gopro/gpmf-parser) ([@gopro](https://github.com/gopro))
  - [OpenCV](https://github.com/opencv/opencv) ([@opencv](https://github.com/opencv))
  - [libyaml-cpp](https://github.com/jbeder/yaml-cpp) ([@jbeder](https://github.com/jbeder))