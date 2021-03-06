# Resolving Libraries  
[From the docs](https://docs.python.org/2/library/sys.html#sys.path), ```sys.path``` is populated using the current working directory, followed by directories listed in your PYTHONPATH environment variable, followed by installation-dependent default paths, which are controlled by the site module.

Look at the paths:  
```  
> import sys
> print '\n'.join(sys.path)  
```


# Prettier dir() Output
Instead of this output:  
```  
>>> dir(importlib)
['_RELOADING', '__all__', '__builtins__', '__cached__', '__doc__', '__file__', '__import__', '__loader__', '__name__', '__package__', '__path__', '__spec__', '_bootstrap', '_bootstrap_external', '_imp', '_r_long', '_w_long', 'abc', 'find_loader', 'import_module', 'invalidate_caches', 'machinery', 'reload', 'sys', 'types', 'util', 'warnings']
>>> print( '\n'.join(dir(importlib)))

```
Use print with join
```
>>> print( '\n'.join(dir(importlib)))
_RELOADING
__all__
__builtins__
__cached__
__doc__
__file__
__import__
__loader__
__name__
__package__
__path__
__spec__
_bootstrap
_bootstrap_external
_imp
_r_long
_w_long
abc
find_loader
import_module
invalidate_caches
machinery
reload
sys
types
util
warnings
```
# Logging
Each module should contain logging.  The file should start with:  
```
import logging
logger = logging.getLogger(__name__)
```
The main file should set at least ```basicConfig```.  e.g.:
```
import logging 
logging.basicConfig(level=logging.DEBUG)
```
So that logging is enabled.

# See stackoverflow https://stackoverflow.com/questions/4730435/exception-passing-in-python