# Chap 2: Kubernetes
 ## Vagrant
  * Vagrant là cái vẹo gì? => Là công cụ tạo ra các máy ảo y chang hyper-v, VMWare hoặc VirtualBox.
  ### Một số định nghĩa trong Vagrant
        1. Vagrantfile: chứa nội dung hướng dẫn cấu hình.
  ### Vagrant basic command
        1. vagrant init => Sinh file cấu hình máy ảo mới Vagrantfile
        2. vagrant up => Thực hiện tạo / hoặc chạy máy ảo với cấu hình từ Vagrantfile
        3. vagrant ssh => Kết nối ssh vào máy ảo, tài khoản kết nối là vagrant
        4. vagrant halt => Dừng máy ảo (shutdown)
        5. vagrant reload => Khởi động lại máy ảo, có đọc lại cấu hình trong Vagrantfile
        6. vagrant destroy => Xóa máy ảo
  ### Setup on Centos 7
        1. sudo yum –y install epel-release
        2. sudo yum –y install gcc dkms make qt libgomp patch
        3. sudo yum –y install kernel-headers kernel-devel binutils glibc-headers glibc-devel font-forge
        4. =================Install VirtualBox============================
        5. sudo yum install kernel-devel kernel-devel-$(uname -r) kernel-headers kernel-headers-$(uname -r) make patch gcc
        6. sudo yum -y install wget
        7. cd /etc/yum.repos.d
        8. sudo wget http://download.virtualbox.org/virtualbox/rpm/rhel/virtualbox.repo
        9. sudo yum –y install VirtualBox-5.2
        10. =================Install Vagrant===============================
        11. sudo wget https://releases.hashicorp.com/vagrant/2.2.2/vagrant_2.2.2_x86_64.rpm
        12. sudo yum –y localinstall vagrant_2.2.2_x86_64.rpm
        13. ---Khởi tạo
        14. sudo mkdir vagrant
        15. cd vagrant/
        16. vagrant init
  ### Bonus: Sample - Setup basic VM
     # -*- mode: ruby Tạo 1 folder chứa các cấu hình trên: 
     # vi: set ft=ruby :

     Vagrant.configure(2) do |config|                # Bắt đầu khai báo máy ảo
        config.vm.box = 'centos/7'                    # Sử dụng Box centos/7 tạo máy ảo

        config.vm.provider "virtualbox" do |vb|       # Máy ảo dùng nền tảng virtualbox, với các cấu hình bổ sung thêm cho provider
            vb.name = "may-ao-01"                      # đặt tên máy ảo tạo ra
            vb.cpus = 2                                # cấp 2 nhân CPU
            vb.memory = "2048"                         # cấu hình dùng 2GB bộ nhớ
        end                                        # hết cấu hình provider
     end                                        #  hết cấu hình tạo máy ảo
  ### Setup kubernetes cluster
   * Phần này sẽ tạo ra một Cluster K8s hoàn chỉnh từ 3 servers chạy CentOS. Thông tin như sau:  
   
|Tên máy/Hostname  |Thông tin hệ thống  |Vai trò  |
|---------|---------|---------|
|master.xtl     |    	HĐH CentOS7, Docker CE, Kubernetes. Địa chỉ IP 172.16.10.100     |    Khởi tạo là master     |
|worker1.xtl     |    HĐH CentOS7, Docker CE, Kubernetes. Địa chỉ IP 172.16.10.101     |    	Khởi tạo là worker     |
|worker2.xtl     |     	HĐH CentOS7, Docker CE, Kubernetes. Địa chỉ IP 172.16.10.102    |     Khởi tạo là worker    |
   
  * Baby Let's go: 
    1. Tạo 1 folder chứa các cấu hình trên: sudo mkdir kubernetes
    2. cd kubernetes/
    3. sudo mkdir master
    4. cd master/
    5. Thiết Vagrantfile cho master như sau:
            
            # -*- mode: ruby -*-
            # vi: set ft=ruby :

            # Tạo máy ảo từ box centos/7, gán địa chỉ IP, đặt hostname, gán 2GB bộ nhớ, 2 cpus  
            Vagrant.configure("2") do |config|
            config.vm.box = "centos/7"
            config.vm.network "private_network", ip: "172.16.10.100"
            config.vm.hostname = "master.xtl"

            config.vm.provider "virtualbox" do |vb|
                vb.name = "master.xtl"
                vb.cpus = 2
                vb.memory = "2048"
            end
            
            # Chạy file install-docker-kube.sh sau khi nạp Box
            config.vm.provision "shell", path: "./../install-docker-kube.sh"

            # Chạy các lệnh shell
            config.vm.provision "shell", inline: <<-SHELL
                # Đặt pass 123456789 có tài khoản root và cho phép SSH
                echo "123456789" | passwd --stdin root
                sed -i 's/^PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config
                systemctl reload sshd
            # Ghi nội dung sau ra file /etc/hosts để truy cập được các máy theo HOSTNAME
            cat >>/etc/hosts<<EOF
            172.16.10.100 master.xtl
            172.16.10.101 worker1.xtl
            172.16.10.102 worker2.xtl
            EOF

            SHELL
            end
    6. cd ..
    7. sudo nano install-docker-kube.sh
            #!/bin/bash

            # Cập nhật 12/2019

            # Cai dat Docker
            yum install -y yum-utils device-mapper-persistent-data lvm2
            yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
            yum update -y && yum install docker-ce-18.06.2.ce -y
            usermod -aG docker $(whoami)

            ## Create /etc/docker directory.
            mkdir /etc/docker

            # Setup daemon.
            cat > /etc/docker/daemon.json <<EOF
            {
            "exec-opts": ["native.cgroupdriver=systemd"],
            "log-driver": "json-file",
            "log-opts": {
                "max-size": "100m"
            },
            "storage-driver": "overlay2",
            "storage-opts": [
                "overlay2.override_kernel_check=true"
            ]
            }
            EOF

            mkdir -p /etc/systemd/system/docker.service.d


            # Restart Docker
            systemctl enable docker.service
            systemctl daemon-reload
            systemctl restart docker


            # Tat SELinux
            setenforce 0
            sed -i --follow-symlinks 's/^SELINUX=enforcing/SELINUX=disabled/' /etc/sysconfig/selinux

            # Tat Firewall
            systemctl disable firewalld >/dev/null 2>&1
            systemctl stop firewalld

            # sysctl
            cat >>/etc/sysctl.d/kubernetes.conf<<EOF
            net.bridge.bridge-nf-call-ip6tables = 1
            net.bridge.bridge-nf-call-iptables = 1
            EOF
            sysctl --system >/dev/null 2>&1

            # Tat swap
            sed -i '/swap/d' /etc/fstab
            swapoff -a

            # Add yum repo file for Kubernetes
            cat >>/etc/yum.repos.d/kubernetes.repo<<EOF
            [kubernetes]
            name=Kubernetes
            baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
            enabled=1
            gpgcheck=1
            repo_gpgcheck=1
            gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
            EOF

            yum install -y -q kubeadm kubelet kubectl

            systemctl enable kubelet
            systemctl start kubelet

            # Configure NetworkManager before attempting to use Calico networking.
            cat >>/etc/NetworkManager/conf.d/calico.conf<<EOF
            [keyfile]
            unmanaged-devices=interface-name:cali*;interface-name:tunl*
            EOF 
    8. chmod +x install-docker-kube.sh
    9. cd master/
    10. vagrant plugin install vagrant-vbguest
    11. sudo yum install rsync
   
    
   
    