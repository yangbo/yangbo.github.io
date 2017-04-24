---
layout: post
title:  "How to install caffe on Ubuntu 16.04"
date:   2017-03-18 09:10:00
categories: deep-learning Caffe
---

# git clone

	git clone <caffe git repository>
	
# Install dependencies

	sudo apt-get update
	
	sudo apt-get upgrade
	
	sudo apt-get install -y build-essential cmake git pkg-config
	
	sudo apt-get install -y libprotobuf-dev libleveldb-dev libsnappy-dev libhdf5-serial-dev protobuf-compiler
	
	sudo apt-get install -y libatlas-base-dev 
	
	sudo apt-get install -y --no-install-recommends libboost-all-dev
	
	sudo apt-get install -y libgflags-dev libgoogle-glog-dev liblmdb-dev

# Compile

	make all -j 6
	make test -j 6
	make pycaffe -j 6
	make runtest -j 6
	make distribute

# Add PYTHONPATH

edit ~/.bash_profile to add below lines.

	export CAFFE_HOME=/home/yangbo/gits/caffe/distribute-p2/
	export PYTHONPATH=$CAFFE_HOME/python
	
	export PATH=/usr/local/cuda-8.0/bin${PATH:+:${PATH}}
	export PATH=$CAFFE_HOME/bin:$PATH
	
	export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
	export LD_LIBRARY_PATH=$CAFFE_HOME/lib${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}

# Install lmdb on Python2

	python2 -measy_install lmdb
	
# Reference

- [Install guide](http://caffe.berkeleyvision.org/install_apt.html)

- [Ubuntu 16.04 install guide](https://github.com/BVLC/caffe/wiki/Ubuntu-16.04-or-15.10-Installation-Guide)
