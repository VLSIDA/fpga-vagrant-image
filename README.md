To start:
* Install vagrant https://www.vagrantup.com/
* Clone this:
```
git clone  git@github.com:VLSIDA/fpga-vagrant-image.git
cd fpga-vagrant-image
```
* Create a shared data directory:
```
mkdir data
```
Note, for WSL, you must put this in /mnt/c for shared filesystems. See the Vagrantfile.
* Run vagrant:
```
vagrant up
vagrant ssh
```
* Clone icebreaker (or another project):
```
cd /vagrant_data
git clone https://github.com/icebreaker-fpga/icebreaker-examples
cd icebreaker_examples/7seg_count
```
* Compile!
```
make
```