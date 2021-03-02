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
     # -*- mode: ruby -*-
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