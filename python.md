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

Empty __init__.py in folder 
    
    Just there to tell Python that it's a package.
    
DRY principle (Don't repeat yourself):
1. Write functions for a task that is performed over and over.
2. Create loops that iterative over repetitive tasks.
3. Use conditional statements to control if and when code is executed.

idiomatic python,awk

* https://gist.github.com/dpallot/1aadff223f3b3efbec8e
    
Readable code, style guide (PEP), my code isnt working
    
* https://pythonforbiologists.com/3-steps-to-readable-code
* https://www.python.org/dev/peps/pep-0008/
* https://www.dropbox.com/s/cqsxfws52gulkyx/drawing.pdf

Python skeleton/template for apps

* https://realpython.com/python-application-layouts/
* https://github.com/dinoboff/skeleton
    
setting up jupyter notebook on a compute node (https://oncomputingwell.princeton.edu/2018/05/jupyter-on-the-cluster/)

    ## on compute node
        module load anaconda3
    
    # For Jupyter Lab:
        jupyter-lab --no-browser --port=8889 --ip=0.0.0.0

    # For Jupyter Notebook:
        jupyter-notebook --no-browser --port=8889 --ip=0.0.0.0
     
    # get hostname
        hostname
    
    ## on local machine
        ssh -N -f -L 8889:<hostname>:8889 <yourusername>@<hpc remote server>
    
    # to kill background ssh tunnel
    lsof -i tcp:8889 # get PID
    kill -9 <PID>
    
 Running bash commands
    
    import subprocess
    import shlex
    
    cmd = 'aws s3 cp --recursive {} {}'.format(s3_path, directory_to_download)
    subprocess.check_call(shlex.split(cmd))
    
 * https://www.logilab.org/blogentry/20469
 
pandas groupby value_counts

    df = pd.DataFrame([
        (1, 1, 'term1'),
        (1, 2, 'term2'),
        (1, 1, 'term1'),
        (1, 1, 'term2'),
        (2, 2, 'term3'),
        (2, 3, 'term1'),
        (2, 2, 'term1')
    ], columns=['id', 'group', 'term'])
    
    df.groupby(['id', 'group', 'term']).size().unstack(fill_value=0)
    
    # alternative
    df.pivot_table(index=['id','group'], columns='term', aggfunc='size', fill_value=0)

* https://stackoverflow.com/questions/39132742/groupby-value-counts-on-the-dataframe-pandas

strategy for reading large csv pandas
    
   * Chunking your data
   
    import pandas as pd
    
    # reach chunk
    data_iterator = pd.read_csv("large_data.csv", chunksize=100000)

    chunk_list = []  

    # Each chunk is in dataframe format
    for data_chunk in data_iterator:
        # process chuck and save results
        filtered_chunk = chunk_filtering(data_chunk)
        chunk_list.append(filtered_chunk)
    
    # combine chunks
    filtered_data = pd.concat(chunk_list)
    
   * Dropping data
   
    use_cols = ["stock_price", "stock_volume", "stock_symbol", "dividend", "eps"]
    ignore_cols = ["stock_name", "data_of_ipo"]

    df = pd.read_csv("large_data.csv", usecols=use_cols) 

   * Set specific data types for each column

    df = pd.read_csv("large_data.csv", dtype={'column_A': np.int32, 'column_B': np.float16})
