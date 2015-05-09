# Table of contents #

# Installation #

Before installing the **engine\_cudamrg** you MUST install the NVIDIA CUDA Toolkit as described in the official CUDA documentation and install the OpenSSL toolkit.

Installing **engine\_cudamrg** is quite simple:
```
wget http://engine-cuda.googlecode.com/files/engine_cudamrg-v_0.1.1.tar.gz
tar xvzf engine_cudamrg-v_0.1.1.tar.gz 
cd engine_cudamrg/
./configure 
make 
make install
```
~~If you install a driver different from the one provided by the NVIDIA web site you MUST specify the path to the `libcuda.so` library on the `configure` command-line.~~

~~For example: if you have the library into the directory `/usr/lib64/nvidia-current` you MUST add to the command-line something like this `LDFLAGS=-L/usr/lib64/nvidia-current`, in other case the script will fail.~~

If your distribution has a version of gcc above 4.4 and a version of the CUDA Toolkit below 3.1 you MUST specify on the `configure` command-line the path to a version of gcc below 4.4 throutgh the `--with-ccbin option`.

For example: if you have gcc-4.3 installed into `/usr/bin` you MUST add to the command-line something like this `--with-ccbin=/usr/bin/gcc-4.3`, in other case the script will fail.

By default the **engine\_cudamrg** installs into `/usr/local/lib/engines`, if you want to install the engine into a different directory you must specify the path through the `--prefix` option on the `configure` command-line.

For example: if you want to install the engine into the directory `/opt` you must specify on the command-line something like this `--prefix=/opt`.

Alternatevely you can download a more recent version from the svn repository using the `svn` command instead of `wget`:
```
svn checkout http://engine-cuda.googlecode.com/svn/trunk/ engine-cudamrg
```
# Configuration #
## Using Engine\_cudamrg with the openssl command ##

You can run the OpenSSL command shell, load the engine and then run any command using the engine.

Here is an example:
```
$ openssl
OpenSSL> engine -t dynamic -pre SO_PATH:/usr/local/lib/engines/libcudamrg.so -pre ID:cudamrg -pre LIST_ADD:1 -pre LOAD
OpenSSL> enc -engine cudamrg -e -aes-256-ecb -k $YOUR_KEY -in $YOUR_INPUT_FILE -out $YOUR_OUTPUT_FILE -bufsize $BUFFER_SIZE
```
In this example the **engine\_cudamrg** is loaded.

The second command encrypt a file named $YOUR\_INPUT\_FILE into $YOUR\_OUTPUT\_FILE using $YOUR\_KEY and a buffer of size $BUFFER\_SIZE.

## Using Engine\_cudamrg with the openssl config file ##

You can create or edit an openssl config file, so you don't need to type in or paste the above commands every the time.

Here is an example for OpenSSL 0.9.8:
```
openssl_conf = openssl_init

[openssl_init]
engines = engine_section

[engine_section]
cudamrg = cudamrg_section

[cudamrg_section]
engine_id = cudamrg
dynamic_path = /usr/local/lib/engines/libcudamrg.so
default_algorithms = ALL
init = 1
```

With such a config file you can directly call openssl to use that engine...
```
openssl enc -engine cudamrg -e -aes-256-ecb -k $YOUR_KEY -in $YOUR_INPUT_FILE -out $YOUR_OUTPUT_FILE -bufsize $BUFFER_SIZE
```
...or...
```
openssl -e -aes-256-ecb -k $YOUR_KEY -in $YOUR_INPUT_FILE -out $YOUR_OUTPUT_FILE -bufsize $BUFFER_SIZE
```

## Using Engine\_cudamrg with the OpenSSL autoloading ##

OpenSSL 0.9.8+ can automaticaly load engines.

If you want to use this feature, add a symlink from `libcudamrg.so` in the `lib/engines/` directory or install it directly into that directory.

After that you can directly call openssl to use this engine...
```
openssl enc -engine cudamrg -e -aes-256-ecb -k $YOUR_KEY -in $YOUR_INPUT_FILE -out $YOUR_OUTPUT_FILE -bufsize $BUFFER_SIZE
```

## Engine\_cudamrg Options ##

Options you can use with **engine\_cudamrg**:
  * SO\_PATH: Specifies the path to the 'cuda-engine' shared library
  * VERBOSE: Print additional details
  * QUIET: Remove additional details
  * BUFFER\_SIZE: Specifies the size of the buffer between central memory and GPU memory in kilobytes (default: 8MB)

**WARNING:** if you use these command with the `-post` option in an OpenSSL command shell or into the configuration file you will get an error like this:
`Error: you cannot set command 201 when the engine is already initialized.[Failure]: VERBOSE`.