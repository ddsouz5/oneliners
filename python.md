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
    
   * https://www.python.org/dev/peps/pep-0420/ (See examples)
   

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

    # Fibonacci recursive caching (original recursive is slow as intermediate results are not saved!; from Python for Finance) 

    from functools import lru_cache as cache
    
    @cache(maxsize=None)  1
    def fib_rec_py2(n):
        if n < 2:
           return n
        else:
           return fib_rec_py2(n - 1) + fib_rec_py2(n - 2)

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
    
   * read_csv() with low_memory=False 
    
   * If your data is mostly numeric (i.e. arrays or tensors), you may consider holding it in a HDF5 format (see PyTables), which lets you conveniently read only
   the necessary slices of huge arrays from disk.
   
   * Use an SQL database. You'll be able to index columns, do basic aggregations via SQL, and get the needed subsamples into Pandas for more complex processing using a simple pd.read_sql. 
   
   * If data file with many zeros or same values, consider sparse matrix; requires much less memory
   
   * https://towardsdatascience.com/3-simple-ways-to-handle-large-data-with-pandas-d9164a3c02c1
   * https://datascience.stackexchange.com/questions/27767/opening-a-20gb-file-for-analysis-with-pandas

Sort every column in pandas df

    df.transform(np.sort)
    
pygsheet

   * https://github.com/nithinmurali/pygsheets
   
Detect duplicates numpy

    import numpy as np
    from collections import Counter
    a = np.array([1, 2, 1, 3, 3, 3, 0])
    [item for item, count in Counter(a).iteritems() if count > 1]   
    > [1, 3]
    
    # using pandas
    u, c = np.unique(a, return_counts=True)
    dup = u[c > 1]
    
   * https://stackoverflow.com/questions/11528078/determining-duplicate-values-in-an-array
  
Recursion

    # Looking for repetitious keys in nested dictionary
    
    def findKey(obj, key):
        for k, v in obj.items():
            if isinstance(v, dict):
                findKey(v, key)
            else:
            if key in obj:
                print(obj[key])
     
    myDic = {"A":{"A": 1, "B":{"B": 2, "C":{"C": 3}}}, "D": 4}
    findKey(myDic, "A")
    > 1
    findKey(myDic, "B")
    > 3
    
    # on lists
    
    findNext = lambda x, obj: -1 if len(obj) == 0 \
        or len(obj) == 1 \
        else obj[1] if x == obj[0] \
        else findNext(x, obj[1:])
    findPrev = lambda x, obj: -1 if len(obj) == 0 \
        or len(obj) == 1 \
        else obj[0] if x == obj[1] \
        else findPrev(x, obj[1:])
    
    myList = [1,2,3,4,5,6]
    findNext(4, myList)
    > 5
    findPrev(4, myList)
    > 3
    findNext(4, [])
    > -1
    findNext(4, [3,4]
    > -1
   
   * Functional program for dummies
    
Passing functions in Python

    def doAdd(x, y):
        return x + y
    def doSub(x, y):
        return x - y
    def compareWithHundred(function, x, y):
        z = function(x, y)
        out = lambda x: "GT" if 100 > x \
        else "EQ" if 100 == x else "LT"
        return out(z)
    
    doAdd(99,2)
    > 101
    doSub(99,2)
    > 97
    compareWithHundred(doAdd, 99, 2)
    > 'LT'
    compareWithHundred(doSub, 99, 2)
    > 'GT'
    compareWithHundred(doAdd, 99, 1)
    > 'EQ'
    
   * Functional program for dummies

