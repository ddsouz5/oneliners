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

###### Name spaces and scope, Python follows LEGB rule when it tries to get variable value

    Local -> Enclosed -> Global -> Built-in

* https://stackoverflow.com/questions/44782031/using-function-parameter-names-that-are-the-same-as-passed-variables
* http://sebastianraschka.com/Articles/2014_python_scope_and_namespaces.html

###### modules vs classes

* https://dbader.org/blog/6-things-youre-missing-out-on-by-never-using-classes-in-your-python-code

TLDR
* Use classes as blueprints for objects that model your problem domain, create exceptions, OOP design patters
* Use modules to collect functionality into logical units

###### Python caching

* https://levelup.gitconnected.com/faster-code-with-python-caching-8da6e8a92ae9

###### Date Time

    import datetime
    x = datetime.datetime.now()
    print(x.strftime("%Y%m%d")) #20200723

executing modules as scripts

    def main():
        print('hello")
    
    if __name__ == '__main__':
        main()
    
    # on cmd line
    python script.py
    
* https://docs.python.org/3/tutorial/modules.html#executing-modules-as-scripts

catch all errors 

    import sys

    try:
        f = open('myfile.txt')
        s = f.readline()
        i = int(s.strip())
    except OSError as err:
        print("OS error: {0}".format(err))
    except ValueError:
        print("Could not convert data to an integer.")
    except:
        print("Unexpected error:", sys.exc_info()[0])
        raise
        
* https://docs.python.org/3/tutorial/errors.html#handling-exceptions

get class and module attributes

    dir(myclass)
    
Hiding credentials, config from users

* https://stackoverflow.com/questions/34097778/i-dont-understand-what-the-point-is-for-having-a-config-py-to-hide-my-secret-ke
* https://stackoverflow.com/questions/34230673/hide-login-credentials-in-python-script
