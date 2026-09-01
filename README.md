# The tuxifier-python conda environment

The `tuxifier-python` environment is a [conda python environment](https://conda-forge.org/docs/) with the minimum requirements for [ansible](https://docs.ansible.com/) to partition and install an OS.<br> It is build for the [tuxifier](https://github.com/Geertsky/tuxifier) ansible collection. An example playbook repository can be found here: [tuxifier-playbook](https://github.com/Geertsky/tuxifier-playbook).<br>
A prebuild `tuxifier-python.squashfs` is available for download from here: [tuxifier-python.squashfs](https://verweggistan.eu/tuxifier-python.squashfs) <br>
It contains, not exclusively, the following:
* coreutils
* curl
* dosfstools
* e2fsprogs
* parted
* pip
* pyparted
* python
* rpm-tools
* util-linux

This is quite minimal, but it serves to show the intention.

For the people that would like to improve or extend the functionality of this python environment, below the steps to build it.
## Installing conda

On the following website is described how to install conda: https://conda-forge.org/download/

## Conda prerequisite - geertsky channel
There are three conda modules created for `tuxifier-python` and some additional changes to existing conda modules.
Not all these changes have been merged yet to conda-forge.
The `geertsky` channel contains these changes as well as the `tuxifier` specific modules.
For building the `tuxifier-python` environment the `geertsky` anaconda channel needs as addition to the `conda-forge` channel.
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
Additionally, the `conda-pack` conda package needs to be installed in the base environment to pack the `tuxifier-python` conda environment in a squashfs.
This can be installed using the following command:

```bash
conda install -n base conda-pack
```

## Building the tuxifier-python environment

In the dracut-tuxifier repository there is a conda environment file which can be use to build the tuxifier-python environment.
Using the following command we can build the `tuxifier-python` environment:

```bash
conda create -f tuxifier-python/tuxifier-python-environment.yml
```

Per default, the `tuxifier-python` conda environment will be placed in `~/miniforge3/envs/tuxifier-python/`.

This environment can be activated for inspection using:
```sh
conda activate tuxifier-python
```
## Packing the tuxifier-python environment

To pack the `tuxifier-python` conda environment we need to use the following command:

```bash
conda-pack --compress-level 9 -j 8  --dest-prefix /local/conda/envs/tuxifier-python --format squashfs -n tuxifier-python
```
_`--compression-level 9` is needed to use xz compression. The only one supported by RHEL8_<br>
_`--dest-prefix /local/conda/envs/tuxifier-python` is needed as the environment gets mounted under `/local/conda/envs/tuxifier-python` in the initramfs._<br>
_`--format squashfs` The environment needs to be packed in a squashfs._<br>

Once `conda-pack` is finished, we have a file `tuxifier-python.squashfs` containing the tuxifier-python environment.
This packed environment needs to be available in the dracut module directory of `dracut-tuxifier`. This is `/lib/dracut/modules.d/94tuxifier`

```bash
sudo mv tuxifier-python.squashfs /lib/dracut/modules.d/94tuxifier
```

Now we're ready to build the initramfs. See: [dracut-tuxifier](https://github.com/Geertsky/dracut-tuxifier)
## tuxifier-python devel branch

The intention of this `tuxifier` project is to be as closely as possible compatible to the redhat anaconda kickstart installer.
The idea is to replace the current LVM partitioning using parted by using the `Storage` role of the `RHEL System Roles` collection.
To realize this the `blivet` python module needs to be available in the `tuxifier-python` conda environment. The `devel` branch contains<br>
the work to get `blivet` into `tuxifier-python'.
