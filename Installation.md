
## Precompiled binaries
The easiest way to get SPLASH is to use [precompiled release](https://github.com/refresh-bio/SPLASH/releases).
To get version 2.11.6 and run the example, is is sufficient to do the following:
```
curl -L https://github.com/refresh-bio/SPLASH/releases/download/v2.11.6/splash-2.11.6.linux.x64.tar.gz | tar xz
cd example
./run-example.sh
```

## Docker container
It is possible to run splash using the docker container available through GitHub packages (https://github.com/orgs/refresh-bio/packages/container/package/splash).

To pull the image run:
```
sudo docker pull ghcr.io/refresh-bio/splash:2.11.6 # replace version number if needed
```
Example of how to run splash with docker.
Prerequisites:
 * There is `input.txt` in the current directory
 * All input splash files defined in the `input.txt` are in the same or child directory
```
sudo docker run -v `pwd`:/home/ubuntu ghcr.io/refresh-bio/splash:2.11.6 splash input.txt
```

## Singularity container
Sometimes, `sudo` is unavailable (for example, on HPC).
In such a case docker container may be transformed into a singularity container (https://docs.sylabs.io/guides/latest/user-guide/).

To pull the singularity version of splash use:
```
singularity pull docker://ghcr.io/refresh-bio/splash:2.11.6 # replace version as needed
```
This will result in a `splash_2.11.6.sif` file created in the current directory.
To execute splash using this file run:
```
./splash_2.11.6.sif splash input.txt
```
It is also possible to run without pulling first:
```
singularity run docker://ghcr.io/refresh-bio/splash:2.11.6 splash input.txt
```
It may be necessary to configure bind for singularity (`-B` parameter) depending on your configuration.

## Compile from sources
SPLASH is implemented as several applications written in the C++ programming language and a Python wrapper to run the whole pipeline.
A compiler supporting C++20 is needed to compile the code.
You can use the following snippet to compile SPLASH.
```
git clone --recurse-submodules https://github.com/refresh-bio/splash
cd splash
make -j
```
After this splash may be run as follows:
```
bin/splash # this will print help
```

#### Install the compiled splash
The simplest way of installing splash after compilation is to run:
```
sudo make install
```
If the `PREFIX` environment variable is not defined the above will install splash in `/usr/local/bin` which requires `sudo`.
It is possible to override the install location so that `sudo` is not required.
For example, to install in the user's home directory, one may run:
```
make install PREFIX=~/splash
```
or
```
export PREFIX=~/splash
make install
```

#### Uninstall splash
To uninstall splash one may run:
```
sudo make uninstall
```
The same `PREFIX` should be used like for installation (depending on this `sudo` may be not required).

## Running the example
To verify the installation on a small example, one may perform the following:
```
cd example
./download.py #download exemplary data
splash input.txt #run the pipeline with default parameters
```
The result consists of two TSV files, namely,
 1. `result.after_correction.all_anchors.tsv` 
 2. `result.after_correction.scores.tsv`
The first file contains all unfiltered anchors found by the pipeline.
The second file contains only anchors whose corrected p-value is below 0.05.
