# Chap 3: Kubernetes
 
  ### Setup kubernetes cluster
   * Phần này sẽ tạo ra một Cluster K8s hoàn chỉnh từ 3 servers chạy CentOS. Thông tin như sau:  
   
| Tên máy/Hostname | Thông tin hệ thống                                           | Vai trò            |
| ---------------- | ------------------------------------------------------------ | ------------------ |
| master.xtl       | HĐH CentOS7, Docker CE, Kubernetes. Địa chỉ IP 172.16.10.100 | Khởi tạo là master |
| worker1.xtl      | HĐH CentOS7, Docker CE, Kubernetes. Địa chỉ IP 172.16.10.101 | Khởi tạo là worker |
| worker2.xtl      | HĐH CentOS7, Docker CE, Kubernetes. Địa chỉ IP 172.16.10.102 | Khởi tạo là worker |
   
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
   
    
   
    