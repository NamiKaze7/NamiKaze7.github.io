Q：

```vhdl
    CommandNotFoundError: Your shell has not been properly configured to use 'conda activate'.
To initialize your shell, run

    $ conda init <SHELL_NAME>

Currently supported shells are:
  - bash
  - fish
  - tcsh
  - xonsh
  - zsh
  - powershell

See 'conda init --help' for more information and options.

IMPORTANT: You may need to close and restart your shell after running 'conda init
```

A：

```bash
source ~/anaconda3/etc/profile.d/conda.sh
conda activate my_env
```





Q：

```vhdl
Error stack (short):
OSError: /lib64/libstdc++.so.6: version `GLIBCXX_3.4.21' not found (required by anaconda3/envs/pytorch-geometric/lib/python3.8/site-packages/torch_scatter/_scatter.so)
```

A:

```shell
conda install -c omgarcia gcc-6 # install GCC version 6
conda install libgcc            # install conda gcc tools

# make sure that you see GLIBCXX_3.4.xx on the list (which it could not find before)
strings <conda-env-path>/lib/libstdc++.so.6 | grep GLIBCXX

# add it to library paths
export LD_LIBRARY_PATH=<conda-env-path>/lib:$LD_LIBRARY_PATH

python -c "import torch; print(torch.cuda.is_available())"  # make sure CUDA is available
python -c "import torch; print(torch.version.cuda)"         # identify pytorch's CUDA version
nvcc --version                                              # identify system wide CUDA compiler version

# if the nvcc version does not match with the pytorch version, then
# go to the path where CUDA is installed (assuming `ls /usr/local/`) 
# and identify the correct CUDA directory. 
# Add it to the system-wide paths (assuming CUDA 10.1)
export PATH=/usr/local/cuda-10.1/bin:$PATH
export CPATH=/usr/local/cuda-10.1/include:$CPATH
export LD_LIBRARY_PATH=/usr/local/cuda-10.1/lib64:$LD_LIBRARY_PATH

# (OPTIONAL) again make sure that both CUDA versions are the same
python -c "import torch; print(torch.version.cuda)"
nvcc --version

# set the CUDA version as an environment variable.
# Valid options are: cpu, cu92, cu100 or cu101
CUDA=cu101

# now install the libraries
pip install torch-scatter==latest+${CUDA} torch-sparse==latest+${CUDA} -f https://s3.eu-central-1.amazonaws.com/pytorch-geometric.com/whl/torch-1.4.0.html
pip install torch-cluster
pip install torch-spline-conv
pip install torch-geometric
```



Q:

```
error while loading shared libraries: libncurses.so.6
error while loading shared libraries: libtinfow.so.6
```

A:

```
sudo ln -s /lib64/libncurses.so.5 /lib64/libncurses.so.6
sudo ln -s /lib64/libtic.so.5 /lib64/libtinfow.so.6
```

