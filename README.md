## RealScout Deep Learning Images

[Packer](http://packer.io) templates for [Caffe](http://caffe.berkeleyvision.org/) and [TensorFlow](https://www.tensorflow.org/) based EC2 GPU-enabled machine and Docker container images from [RealScout](http://realscout.com).

## Step 0

Download the v5 cuDNN bundle from https://developer.nvidia.com/cudnn and place it in `install/`.

You'll also need:
  * [Packer](https://www.packer.io/downloads.html)
  * [An AWS Account](https://aws.amazon.com/getting-started/) to build [AMIs](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html)
  * [Docker](http://docker.com) to build container images

Finally, clone this repo and run the commands below from the root of this project.  Probably:
```
git clone https://github.com/RealScout/deep-learning-images.git
cd deep-learning-images
```

## Base Image

First create a base image with CUDA and cuDNN libraries installed.  We need a Ubuntu 16.04 AMI to start building up from, the template includes a [recent one](https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#Images:visibility=public-images;imageId=ami-40d28157;sort=creationDate) that can be overriden by passing `-var ubuntu_ami=ami-xxxx` to packer.

To build your base image run: `packer build base.json`.  You should get a message about a successful build including an AMI ID like `ami-xxxxx`.

```
==> amazon-ebs: Uploading install/cudnn-8.0-linux-x64-v5.1.tgz => /tmp/cudnn.tar.gz
==> amazon-ebs: Provisioning with shell script: install/cudnn.sh
==> amazon-ebs: Stopping the source instance...
==> amazon-ebs: Waiting for the instance to stop...
==> amazon-ebs: Creating the AMI: Base 1479906007
    amazon-ebs: AMI: ami-6908e904
==> amazon-ebs: Waiting for AMI to become ready...
```

Note that AMI ID, here `ami-6908e904`, we'll need it in the next step.

## Base++ Images

Now follow instructions for your favorite deep learning package.  You could combine them and make one enormous image with all of them installed.

Each of these templates will also build Docker container images.  To prevent that, pass `-only=amazon-ebs` to packer.  Use `-only=docker` to only build the docker images.  *note: docker container images are not GPU-enabled as currently configured.*

### Caffe Image

Caffe version: `HEAD`

To build your caffe image run (substitute the ami id from the base step): `packer build -var base_ami=ami-6908e904 caffe.json`.

### TensorFlow Image

TF Version: `v0.11.0`

To build your tensorflow image run (substitute the ami id from the base step): `packer build -var base_ami=ami-6908e904 tensorflow.json`.
