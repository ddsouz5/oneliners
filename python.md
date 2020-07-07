conda update package

    conda install -c bioconda scanpy=1.5.0
    
get version 

    module.__version__
    
reload module

    import importlib
    importlib.reload(module)

print docstrings and annotation

    print(func.__doc__)
    print(func.__annotations__)
