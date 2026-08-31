# The bambini-python conda environment

The bambini-python environment is a conda environment with all prerequisite python modules available to install a machine with an OS.
A prebuild `bambini-python.squashfs` is available from [here]()
## Installing conda

On the following website is described how to install conda: https://conda-forge.org/download/

## Conda prerequisite - geertsky channel
For building the bambini-python environment the `geertsky` anaconda channel needs as addition to the `conda-forge` channel.
This can be done with the following command:

```bash
conda config --add channels geertsky
```

To see the configured channels we can issue a `conda config --show-sources` which should return:

```
channel_priority: strict
channels:
  - geertsky
  - conda-forge
report_errors: False
```

As an additional test we can issue a `conda search parted` which should return:

```
Loading channels: done
# Name                       Version           Build  Channel
parted                           3.7      h53a3f9b_0  geertsky
```

## Conda prerequisite - conda-pack
Additionally, the `conda-pack` conda package needs to be installed in the base environment.
This can be done using the following command:

```bash
conda install -n base conda-pack
```

## Building the bambini-parted environment

In the dracut-bambini repository there is a conda environment file which can be use to build the bambini-parted environment.
Using the following command we can build the `bambini-python` environment:

```bash
conda create -f dracut-bambini/conda-recipes/bambini-parted-environment.yml
```

## Packing the bambini-python environment

To pack the `ansible-bambini` we need to use the following command:

```bash
conda-pack --compress-level 9 -j 8  --dest-prefix /local/conda/envs/bambini-python --format squashfs -n bambini-python
```
_`--compression-level 9` is needed to use xz compression. The only one supported by RHEL8_
_`--dest-prefix /local/conda/envs/bambini-python` is needed as the environment gets mounted under `/local/conda/envs/bambini-python` in the initrd._
_`--format squashfs` We want the environment packed in a squashfs.

Once `conda-pack` is finished, we have a file `bambini-parted.squashfs` containing the bambini-python environment.
This packed environment needs to be available in the dracut module directory of `dracut-bambini`. This is `/lib/dracut/modules.d/94bambini`

```bash
sudo mv bambini-parted.squashfs /lib/dracut/modules.d/94bambini
```
