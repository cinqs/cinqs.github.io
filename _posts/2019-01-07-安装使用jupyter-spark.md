---
layout: post
title: 安装使用jupyter-spark
---

## 1. prerequests:

  1. installed and functional Spark ([how to install spark](https://spark.apache.org/))
  2. installed and functional Jupyter ([how to install Jupyter](https://jupyter.readthedocs.io/en/latest/))
  3. let's assume some envs for your case:
      ```yml
      SPARK_HOME: /opt/spark/
      python_installation_path: /usr/bin/python
      ```
  
## 2. Add a new kernel for jupyter:

  1. use cmd below to find where jupyter saved its kernels, let assume `/usr/share/jupyter/kernels/` in our case
  ```sh
  jupyter kernelspec list
  ```
  2. mkdir a folder for kernel files
  ```sh
  cd /usr/share/jupyter/kernels/
  mkdir pyspark
  cd pyspark
  ```
  3. copy those three files below to cwd
  
  **kernel.json**
  ```json
  {
  "display_name": "Pyspark", 
  "language": "python",
  "env": {
    "SPARK_HOME": "/opt/spark/",
    "PYTHONPATH": "/usr/share/jupyter/kernels/pyspark:/opt/spark/python:/opt/spark/python/lib/py4j-0.10.7-src.zip",
    "PYTHONSTARTUP": "/usr/share/jupyter/kernels/pyspark/shell.py",
    "PYSPARK_PYTHON": "/usr/bin/python",
    "PYSPARK_DRIVER_PYTHON": "/usr/bin/python"
  },
  "argv": [
    "/usr/bin/python",
    "-m",
    "startup",
    "-f",
    "{connection_file}"
   ]
}
  ```
  
  **startup.py**
  ```py
    # -*- coding: UTF-8 -*-
    import sys
    import os
    
    if __name__ == '__main__':
        connection_file = sys.argv[-1]
        kernel_id = os.path.basename(connection_file)[7:-5]
        with open(os.path.join('/tmp', "jupyter-{}-pid".format(kernel_id)), 'w') as f:
            f.write(str(os.getpid()))
        from ipykernel import kernelapp as app
        app.launch_new_instance()
  ```
  
  **shell.py**
  ```py
    # -*- coding: UTF-8 -*-
    import os
    import requests
    import commands
    
    SPARK_SHELL = '/opt/spark/python/pyspark/shell.py'
    os.environ["PYSPARK_SUBMIT_ARGS"] = "--master yarn-client " \
                                        "--executor-cores 3 " \
                                        "--executor-memory 20G " \
                                        "--driver-memory 4G " \
                                        "--conf spark.speculation=true " \
                                        "--conf spark.sql.filesourceTableRelationCacheSize=0 " \
                                        "--conf spark.shuffle.service.enabled=true " \
                                        "--conf spark.dynamicAllocation.enabled=true " \
                                        "--conf spark.dynamicAllocation.initialExecutors=5 " \
                                        "--conf spark.dynamicAllocation.minExecutors=0 " \
                                        "--conf spark.dynamicAllocation.maxExecutors=600 " \
                                        "--conf spark.dynamicAllocation.executorIdleTimeout=1200 " \
                                        "--conf spark.dynamicAllocation.cachedExecutorIdleTimeout=3600 " \
                                        "--conf spark.speculation=true --conf spark.speculation.interval=10000ms " \
                                        "--conf spark.port.maxRetries=500 " \
                                        "--name my_app_name " \
                                        "--conf spark.hadoop.yarn.cluster.name=default " \
                                        "pyspark-shell "
    
    with open(SPARK_SHELL) as f:
        code = compile(f.read(), SPARK_SHELL, 'exec')
        exec (code)
    with open(os.path.join('/tmp', "jupyter-{}-sparkWebUrl".format(os.getpid())), 'w') as f:
        f.write(sc.uiWebUrl)

  ```
  
## 3. install nbextensions
  
  Normally, you would already have a functional pyspark kernel in your notebook. try to test it by starting a new notebook with kernel "pyspark" in the top-right corner. But if you don't see it, try to restart your jupyter server before any modifcation.

  Mozilla has a nice extension for jupyter to view the progress of your tasks / jobs through Spark Web Url, [check it here](https://github.com/mozilla/jupyter-spark). If you like it, I suggest you move on.
  
  But firstly, you will have to get another python module installed, which is called: [nbextensions](https://jupyter-contrib-nbextensions.readthedocs.io/en/latest/install.html). try to follow them installing it.
  
  And then install `jupyter_spark` following [this](https://github.com/mozilla/jupyter-spark). and don't forget to enable this extension.

## 4. Some customization
  
  After the installation of `jupyter_spark`, you will have a table showing the progresses of the spark jobs. But if you wanna go to the Spark Web UI, you will have three options:
  1. Go there by typing the url, follow https://spark.apache.org/docs/latest/monitoring.html
  2. Go to http://localhost:8888/spark
  3. Modify the progress table contents
  
     Normally, `jupyter_spark` extension javascript file will be saved in three optional locations, depending on your installation process
     
     - `/usr/share/jupyter/nbextensions/jupyter-spark/extension.js` 
     - `/usr/local/share/jupyter/nbextensions/jupyter-spark/extension.js`
     - `~/.local/share/jupyter/nbextensions/jupyter-spark/extension.js`
     find this file and replace one of its function with the one below:
     ```javascript
     var create_application_table = function(e) {
        var application_div = $('<div/>');
        var application_link = $('<a/>').attr('target', '_blank').attr('href', 'http://nodeserver:port/' + e.id);
        var application_display_msg = $('<h5/>').text(e.name + " : " + e.id);
        application_link.append(application_display_msg)
        application_div.append(application_link)
        //application_div.append($('<h5/>').text(e.name + ': ' + e.id));
        var application_table = $('<table/>').addClass('table table-hover');
    
        var header_row = $('<tr/>');
        header_row.append($('<th/>').text('Job ID'));
        header_row.append($('<th/>').text('Job Name'));
        header_row.append($('<th/>').text('Progress'));
        application_table.append(header_row);
    
        e.jobs.forEach(function(job) {
            application_table.append(create_table_row(job));
        });
    
        application_div.append(application_table);
        return application_div;
     };
     ```
     then you would be able to go WebUI directly from jupyter notebook
     
## Done. And I have no pictures.....
  
  
  
