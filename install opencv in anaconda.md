# install anaconda 
```
 https://docs.continuum.io/anaconda/install
```

# Create a virtual environment using conda
```
conda create -n <ENV-NAME> python=<python-version> anaconda
Eg - conda create -n django python=2.7 anaconda
```

# compile opencv with ffmpeg

# cp file to the anaconda site-packages of your virtualenv
```
 ln -s /usr/local/lib/python2.7/dist-packages/cv.py cv.py
 ln -s /usr/local/lib/python2.7/dist-packages/cv2.so sv2.so
 cp /usr/lib/x86_64-linux-gnu/libgomp.so.1 ./
 cp /usr/lib/x86_64-linux-gnu/libstdc++.so.6 ./
```

# test
```
> ipython
In [1]: import cv2

In [2]: import cv
```
