conda update package

    conda install -c bioconda scanpy=1.5.0
    
get version 

    module.__version__ # module version
    import sys; sys.version() # python version within script
    
reload module

    import importlib
    importlib.reload(module)
    
module search path

    import sys
    sys.path
    
    sys.path.append('additional-module-path') # add module path

print docstrings and annotation

    print(func.__doc__)
    print(func.__annotations__)

Name spaces and scope, Python follows LEGB rule when it tries to get variable value

https://stackoverflow.com/questions/44782031/using-function-parameter-names-that-are-the-same-as-passed-variables
http://sebastianraschka.com/Articles/2014_python_scope_and_namespaces.html

    Local -> Enclosed -> Global -> Built-in,

###### modules vs classes

https://dbader.org/blog/6-things-youre-missing-out-on-by-never-using-classes-in-your-python-code

TLDR
* Use classes as blueprints for objects that model your problem domain, create exceptions, OOP design patters
* Use modules to collect functionality into logical units
