# ComputeCanadaViaVsCodeSSH
Just a small crib sheet for getting Jupyter Notebook Working with Fastai on a Cluster (Don't do it if you don't have to)

# Running a Notebook
Based on these instructions which are partially wrong: https://docs.alliancecan.ca/wiki/JupyterNotebook

For FastAI you also need this line (https://fastai1.fast.ai/install.html):

  ```
  pip install jupyter notebook jupyter_contrib_nbextensions
  pip install 'fastai<2.0.0'
  ```
The documentation is wrong for creating the notebook.sh file in the $VIRTUAL_ENV/bin, it should be this:
  
  ```
  #!/bin/bash
  unset XDG_RUNTIME_DIR
  module load python/3.8
  source $HOME/jupyter_py38/bin/activate
  jupyter-notebook --no-browser --ip=0.0.0.0
  ```
## Steps:
- Create the virtual env (as noted in the documentation, first link)
- Once you run the salloc to allocate the compute node use:
  
    `salloc --time=1:0:0 --ntasks=1 --cpus-per-task=2 --mem-per-cpu=1024M --gres=gpu:1 --account=def-vfast srun $VIRTUAL_ENV/bin/notebook.sh`

- This has a the `gpu:1` flag meaning that 1 gpu can be used.
- Then, once it has been allocated, use `ssh username@cedar.computecanada.ca -L 8888:cdr767:8888` in a new terminal to create a tunnel to the server. 
- Note that the `cdr767` has to be the same Compute Node as noted in the `salloc` output in the previous step.

## To kill a local process
Sometimes a port is already open and you need to find and kill it.
Use this to find (where 8888 is the port to find) sudo lsof -i :8888
Use this to kill it kill -9 8888
