## conda

```sh
cp /mnt/dolphinfs/hdd_pool/docker/user/hadoop-grocerygoodsalgo/zhanghaozhou/envs/python/Anaconda3-2020.02-Linux-x86_64.sh ~/
bash ~/Anaconda3-2020.02-Linux-x86_64.sh
cp /mnt/dolphinfs/hdd_pool/docker/user/hadoop-grocerygoodsalgo/zhanghaozhou/envs/python/.condarc ~/.condarc
bash /mnt/dolphinfs/hdd_pool/docker/user/hadoop-grocerygoodsalgo/zhanghaozhou/envs/install_python.sh
conda activate py37kaze
```

## tools

```sh
cd /mnt/dolphinfs/hdd_pool/docker/user/hadoop-grocerygoodsalgo/zhanghaozhou/envs/tools/htop-2.2.0
./configure --prefix=$HOME/htop --disable-shared
make && make install

bash /mnt/dolphinfs/hdd_pool/docker/user/hadoop-grocerygoodsalgo/zhanghaozhou/envs/install_tmux.sh
```

## QA:

- 1
  
  - Q:
  
    ```shell
    /home/hadoop-grocerygoodsalgo/anaconda3/bin/python: /home/hadoop-grocerygoodsalgo/anaconda3/bin/../lib/libuuid.so.1: no version information available (required by /opt/meituan/dolphinfs-client-preload/libdolphinfs_client_preload.so)
    ```
  
    A:
  
    ```shell
    ln -s /usr/lib64/libuuid.so.1 /home/hadoop-grocerygoodsalgo/anaconda3/lib/libuuid.so.1
    ```
  
    
- 2
  
  - Q:
  
    ```
    error while loading shared libraries: libncurses.so.6
    error while loading shared libraries: libtinfow.so.6
    ```
  
    A:
  
    ```
    sudo ln -s /lib64/libncurses.so.5 /lib64/libncurses.so.6
    sudo ln -s /lib64/libtic.so.5 /lib64/libtinfow.so.6
    ```
  