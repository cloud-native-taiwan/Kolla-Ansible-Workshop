# Kolla-Ansible-Workshop
Kolla-Ansible-Workshop Repo 為 OpenStack Taiwan User Group Meetup 中使用來教學參加者使用 Kolla-Ansible 部署 OpenStack

[介紹投影片](https://docs.google.com/presentation/d/1LRGXzHIV8jTosloWS5l6CKYYxxqStq9LKe78wqbb6nk/edit?usp=sharing)可以在此下載或是閱讀 Repo 中的 PDF

本教學是基於[OpenStack官方文件](https://docs.openstack.org/kolla-ansible/latest/contributor/vagrant-dev-env.html)

## 如何開始

我們在 meetup 中使用的 Vagrantfile 可以測試安裝 all-in-one (單節點) 跟 multinode (多節點) 的安裝。預設是會生成單節點

## 安裝Vagrant跟所需套件

Vagrant 可以在其官方網站上[下載](https://www.vagrantup.com/downloads.html)

### 使用KVM

CentOS :

```
sudo yum install ruby-devel libvirt-devel zlib-devel libpng-devel gcc \
qemu-kvm qemu-img libvirt libvirt-python libvirt-client virt-install \
bridge-utils
```

Ubuntu :
```
sudo apt-get install ruby-dev ruby-libvirt python-libvirt libvirt-dev nfs-kernel-server zlib1g-dev libpng12-dev gcc git
```

### 使用 VirtualBox

從 VirtualBox 官方網站上[下載](https://www.virtualbox.org/wiki/Downloads)並安裝


### 安裝 Vagrant Plugin

接下來我們需要安裝 hostmanager plugin 所以所有的 host 將會被記錄在 vm 中的``/etc/host``檔案

```
vagrant plugin install vagrant-hostmanager
```

使用 libvirt 的話我們要安裝 libvirt 的 plugin

```
vagrant plugin install --plugin-version ">= 0.0.31" vagrant-libvirt
```

使用 VirtualBox 的話我們要安裝 VirtualBox 的 plugin

```
vagrant plugin install vagrant-vbguest
```

### 下載 Source Code

在你想要的資料夾底下下載 Kolla 跟 Kolla-Ansible 的 source code

```
git clone https://git.openstack.org/openstack/kolla-ansible
git clone https://git.openstack.org/openstack/kolla
```

### 開啟VM

```
cd kolla-ansible/contrib/dev/vagrant && vagrant up
```
使用者可以根據自己需求編輯 Vagrantfile
預設會開啟基於 **CentOS** 的**單節點環境**


### 使用 VirtualBox

使用 VirtualBox 的話請先將 Vagrantfile 中的

```
PROVIDER ||= "libvirt"
```
改成
```
PROVIDER ||= "virtualbox"
```

儲存檔案後再跑

```
vagrant up
```

## Vagrant Up 完成後

Vagrant Up 運行時會同時安裝必要的套件並且在 **operator** VM 跑一個 docker registry

首先我們要連線到 **operator** node

```
vagrant ssh operator
```

如果有需要重啟虛擬機

```
vagrant reload
```

如果要清空環境

```
vagrant destroy
```

## 編 Kolla Image

登入進 **operator** 之後我們要編譯 Kolla 的 Image

```
kolla-build
```

更多參數可以參考[官方文件](https://docs.openstack.org/kolla/latest/image-building.html)
在 Vagrant 生成的 VM 中不需下參數就會 push 編好的 Image 到 VM 中的 registry

## 使用 Kolla-Ansible 部署 OpenStack

部署**單節點 OpenStack**

```
sudo kolla-ansible deploy
```

部署**多節點 OpenStack**

CentOS :
```
sudo kolla-ansible deploy -i /usr/share/kolla-ansible/ansible/inventory/multinode
```

Ubuntu :
```
sudo kolla-ansible deploy -i /usr/local/share/kolla-ansible/ansible/inventory/multinode
```

完成後可以到
http://172.28.128.254/
連線到 OpenStack Dashboard

## 延伸閱讀

[OpenStack Kolla 官方文件](https://docs.openstack.org/kolla/latest/)

[OpenStack Kolla-Ansible 官方文件](https://docs.openstack.org/kolla-ansible/latest/)
