This is an image to set up an open-source FPGA tool environment in Ubuntu.
It also includes USB pass-through setup for the iCEBreaker and FOMU FPGA boards.

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
vagrant up --provision
```
Now, take a break and get outside while it compiles everything.
* Log in to the image:
```
vagrant ssh
```
* Clone the icebreaker examples:
```
cd /vagrant_data
git clone https://github.com/icebreaker-fpga/icebreaker-examples
cd icebreaker_examples/7seg_count
make
```
* Clone the fomu workshop demo:
```
cd /vagrant_data
git clone --recurse-submodules https://github.com/im-tomu/fomu-workshop.git
cd fomu-workshop/verilog-blink
make FOMU_REV=pvt1
dfu-util -D blink.dfu
```
