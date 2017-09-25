# UnicodeDecodeError: 'ascii' codec can't decode byte 0xe4 in position 44: ordinal not in range(128)
- reason: in python 2.x, default encoding is not 'utf8'.
- solution:  
```
  touch ./lib/python2.7/site-packages/sitecustomize.py
  vim ./lib/python2.7/site-packages/sitecustomize.py  
```
  add following code into sitecustomize.py
```
  # encoding=utf8  
  import sys  

  reload(sys)  
  sys.setdefaultencoding('utf8')
```
