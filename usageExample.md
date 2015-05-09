# Table of contents #


# Usage Example #
In this section I show to you what you need to do to use `engine_cudamrg` in the better way.
## For programmers ##
This section is on my TODO list, I will write it when I have enough time.
## For end users ##
With version 0.1.0 of `engine_cudamrg` you need to rebuild the OpenSSL toolkit modifing two source file with the patch utility for having the speedup that a CUDA enabled device is able to provide.

In the following example we download, modify, build and install the OpenSSL tookit, we use `/opt` as target directory for both the OpenSSL toolkit and the `engine_cudamrg` but you can use any directory you want.
```
 $ wget http://www.openssl.org/source/openssl-0.9.8k.tar.gz
 $ wget http://engine-cuda.googlecode.com/files/engine_cudamrg-v_0.1.1.tar.gz
 $ tar xvzf openssl-0.9.8k.tar.gz 
 $ tar xvzf engine_cudamrg-v_0.1.1.tar.gz 
 $ patch ./openssl-0.9.8k/apps/speed.c ./engine_cudamrg/openssl-patch/openssl-0.9.8.k-apps_speed.c.patch 
 $ patch ./openssl-0.9.8k/crypto/evp/bio_enc.c ./engine_cudamrg/openssl-patch/openssl-0.9.8.k-crypto_evp_bio_c.c.patch 
 $ cd openssl-0.9.8k/
 $ ./config --prefix=/opt
 $ make
 $ sudo make install
 $ cd ../engine_cudamrg/
 $ ./configure --prefix=/opt
 $ make
 $ sudo make install
 $ /opt/bin/openssl engine -vvvv cudamrg -c
```
We can reassume what we have done with the following list:
  1. download both the OpenSSL toolkit and the `engine_cudamrg`
  1. untar the downloaded file
  1. patch the two source file that need modification
  1. configured, compiled and installed the OpenSSL toolkit
  1. configured, compiled and installed the `engine_cudamrg`
  1. sure that everything go well

Please note that in this example we haven't build the OpenSSL toolkit shared library, if you want to build the modified shared library you need to add the option `shared` to the `config` command-line but remember to modify the `LD_LIBRARY_PATH` to include the modified library directory before the original library directory.
```
 $ /opt/bin/openssl speed -engine cudamrg -evp aes-256-ecb
...
```
The designated buffer size will be the one that is highter.
```
 $ /opt/bin/openssl enc -engine cudamrg -e -aes-256-ecb -v -bufsize $DESIGNATED_BUFFER_SIZE -k $KEY -in $IN_FILE -out $OUT_FILE
```
For finding the buffer size that is better suited to your CUDA enabled device please use the modified version of speed that you have build, probably the resulted buffer size will be beatwen 256KB and 2MB depending from your hardware.

You can now use and test the engine but remember to use the buffer size that is better suited to the power of you CUDA enabled device in other case you can experience worst performance than with the CPU.

**NOTE:** in this example I use version 0.9.8k of the OpenSSL toolkit simply because is the one provided with my distribution.
# How to use the test suite #
Using the provided test suite is quite simple.
## Running test-enc ##
The directory `test-enc` contains three shell script that execute the test and writes to disk the result and other four script that plot the result.

If you have the `engine_cudamrg` and the rebuilded version of OpenSSL set up correctly, you must change the variable 'OPENSSL' into the script `test-enc-cpu.sh` and `test-enc-gpu.sh` to point to the openssl executable.

After that you must execute the `test-enc-gpu.sh`, than rebuild the engine with the option `--enable-cpuonly`, execute the script `test-enc-cpu.sh` and execute the script `test-enc-plot.sh` for plotting the result. At the end remember to rebuild the engine without the option `--enable-cpuonly`.

These shell scripts produce as output 24 file, 4 for every cipher (encryption and decryption for both CPU and GPU), at the end you can plot the result if you want.

## Running test-speed ##
The directory `test-speed` contains one shell script that execute the test and writes to disk the result and other four script that plot the result.

If you have the `engine_cudamrg` and the rebuilded version of OpenSSL set up correctly the only thing that you must do is modify the variable 'OPENSSL' into the script to point to the openssl executable.

The shell script produce as output 24 file, 4 for every cipher (encryption and decryption for both CPU and GPU), at the end it plot the result, you can also build tables in html, csv and wiki with the data used to draw the graphs using the script `make-table-speed.sh`.

## Running test-file ##
The directory `test-file` contains one shell script that executes the test and writes to screen the result.

In order to execute correctly the test you MUST specify the variable 'INPUT\_FILE' and 'INPUT\_DIR' to a existing file and at the directory that contains it, optionally you could specify also a name for the output file with the variable 'OUTPUT\_FILE' and specify a buffer size with the variable 'BUFSIZE'.

If you have the `engine_cudamrg` and the rebuilded version of OpenSSL set up correctly you MUST is modify the variable 'OPENSSL' into the script to point to the openssl executable.

The shell`s script encrypts and decrypts the provided file with all supported ciphers and compare the md5 of the decrypted file with the md5 of the original file, temporary file are stored into the directory specified with the variable 'OUTPUT\_DIR'.