# Chap 2: Vagrant ảo hóa
![image info](./images/vagrant.png) 
* Vagrant là một công cụ tương tác với các nền tảng ảo hóa như VirtualBox, HyperV, VM ..., nó giúp tạo và quản lý các máy ảo trên các nền tảng đó. Vagrant cung cấp một cấu hình đơn giản tạo và quản lý, tương tác với máy ảo mà không có nhiều sự khác biệt dù bạn đang sử dụng VirtualBox, VM hay HyperV.

  ## Lưu ý trước khi cài đặt
  * Một số máy ảo của google, AWS, VNG hiện mặc định không mở tính năng ảo hóa hỗ trợ công nghệ **Virtualization**. Nên trước khi thiết lập nên kiểm tra xem có hỗ trợ công nghệ Virtualization không nhé. Có 2 loại là **VT-X(Chip intel) & AMD-V(Chip AMD)**.
  * Windows:   ![image info](./images/5.png) 
  * Linux: **lscpu**  ![image info](./images/6.png) 
  
  ## Let's Go
  * Để ảo hóa chúng ta sẽ cài **virtualbox** để tạo ra máy ảo & cài đặt vargant nha.
    
    ### Cài đặt VirtualBox
      1.  sudo yum -y update
      2.  sudo yum –y install epel-release
      3.  sudo yum –y install gcc dkms make qt libgomp patch
      4.  sudo yum –y install kernel-headers kernel-devel binutils glibc-headers glibc-devel font-forge
      5.  sudo yum -y install wget
      6.  wget https://www.virtualbox.org/download/oracle_vbox.asc
      7.  rpm --import oracle_vbox.asc
      8.  mkdir /etc/yum.repo.d/
      9.  cd /etc/yum.repo.d/      
      10. wget http://download.virtualbox.org/virtualbox/rpm/el/virtualbox.repo -O /etc/yum.repos.d/virtualbox.repo      11. 
      12. yum install -y VirtualBox-6.0
      13. systemctl status vboxdrv #check status
  
    ### Cài đặt Vagrant
      1. sudo yum -y update
      2. sudo yum -y install https://releases.hashicorp.com/vagrant/2.2.6/vagrant_2.2.6_x86_64.rpm
      3. vagrant --version # Check Version
    
    