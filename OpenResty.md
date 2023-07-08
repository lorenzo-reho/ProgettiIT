## INSTALLAZIONE

```bash
sudo apt-get update
sudo apt-get install libpcre3-dev libssl-dev perl make build-essential curl
sudo apt-get zlib1g-dev
wget https://openresty.org/download/openresty-<VERSION>.tar.gz

tar -xvf openresty-<VERSION>.tar.gz
cd openresty-VERSION/

sudo ./configure -j2
sudo make -j2 
sudo make install

# in .profile
export PATH=/usr/local/openresty/bin:/usr/local/openresty/nginx/sbin:$PATH
```