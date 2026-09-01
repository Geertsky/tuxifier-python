# The tuxifier-python conda environment

The tuxifier-python environment is a conda environment with all prerequisite python modules available to install a machine with an OS.
A prebuild `tuxifier-python.squashfs` is available from [here]()
## Installing conda

On the following website is described how to install conda: https://conda-forge.org/download/

## Conda prerequisite - geertsky channel
For building the tuxifier-python environment the `geertsky` anaconda channel needs as addition to the `conda-forge` channel.
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

## Building the tuxifier-parted environment

In the dracut-tuxifier repository there is a conda environment file which can be use to build the tuxifier-parted environment.
Using the following command we can build the `tuxifier-python` environment:

```bash
conda create -f dracut-tuxifier/conda-recipes/tuxifier-parted-environment.yml
```

## Packing the tuxifier-python environment

To pack the `ansible-tuxifier` we need to use the following command:

```bash
conda-pack --compress-level 9 -j 8  --dest-prefix /local/conda/envs/tuxifier-python --format squashfs -n tuxifier-python
```
_`--compression-level 9` is needed to use xz compression. The only one supported by RHEL8_
_`--dest-prefix /local/conda/envs/tuxifier-python` is needed as the environment gets mounted under `/local/conda/envs/tuxifier-python` in the initrd._
_`--format squashfs` We want the environment packed in a squashfs.

Once `conda-pack` is finished, we have a file `tuxifier-parted.squashfs` containing the tuxifier-python environment.
This packed environment needs to be available in the dracut module directory of `dracut-tuxifier`. This is `/lib/dracut/modules.d/94tuxifier`

```bash
sudo mv tuxifier-parted.squashfs /lib/dracut/modules.d/94tuxifier
```
