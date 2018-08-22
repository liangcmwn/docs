# 在 ubuntu 上安装 Docker 
see https://docs.docker.com/install/linux/docker-ce/ubuntu/
## 1. 安装virtualbox
环境准备： ubuntu 16.04
安装ubuntu18.04后安装virtualbox 报依赖无法安装错误

sudo apt-get install virtualbox
## 2. 安装docker
   59  sudo apt-get install     apt-transport-https     ca-certificates     curl     software-properties-common
   60  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
   61  sudo apt-key fingerprint 0EBFCD88
   62  sudo add-apt-repository    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
   63  sudo apt-get update
   64  sudo apt-get install docker-ce
   65  docker version

## 将当前用户加入docker用户组
`sudo usermod -aG docker $USER`

3. 安装 docker-machine
base=https://github.com/docker/machine/releases/download/v0.14.0 &&
  curl -L $base/docker-machine-$(uname -s)-$(uname -m) >/tmp/docker-machine &&
  sudo install /tmp/docker-machine /usr/local/bin/docker-machine
docker-machine version

4. 安装 docker-compose
see： https://docs.docker.com/compose/install/#install-compose
sudo curl -L https://github.com/docker/compose/releases/download/1.22.0/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version