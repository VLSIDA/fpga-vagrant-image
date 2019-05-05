To start:
1) Install vagrant https://www.vagrantup.com/
2) Clone this:
git clone  git@github.com:VLSIDA/fpga-vagrant-image.git
cd fpga-vagrant-image
3) Create a shared data directory:
mkdir data
4) Run vagrant:
vagrant up
5) Clone icebreaker (or another project):
cd /vagrant_data
git clone https://github.com/icebreaker-fpga/icebreaker-examples
cd icebreaker_examples/7seg_count
6) Compile!
make
