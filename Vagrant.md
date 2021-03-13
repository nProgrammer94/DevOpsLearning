# Chap 2: Vagrant
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
    
    ### Sample
      1. mkdir /var/vagrant/ #Tạo 1 thư mục chứa project
      2. cd /var/vagrant/ 
      3. vagrant init # Khởi tạo file Vagrantfile
      4. vagrant plugin install vagrant-vbguest
      5. yum -y install rsync
      6. Thiết Vagrantfile như sau: 
   
                # -*- mode: ruby -*-
                # vi: set ft=ruby :

                Vagrant.configure(2) do |config|                # Bắt đầu khai báo máy ảo
                  config.vm.box = 'centos/7'                    # Sử dụng Box centos/7 tạo máy ảo

                  config.vm.provider "virtualbox" do |vb|       # Máy ảo dùng nền tảng virtualbox, với các cấu hình bổ sung thêm cho provider
                    vb.name = "may-ao-01"                      # đặt tên máy ảo tạo ra
                    vb.cpus = 2                                # cấp 2 nhân CPU
                    vb.memory = "2048"                         # cấu hình dùng 2GB bộ nhớ
                  end                                           # hết cấu hình provider

                end                                             #  hết cấu hình tạo máy ảo
      7. vagrant up # Khởi tạo máy ảo dựa trên file **Vagrantfile**
      8. Khi máy ảo đang chạy, vẫn đang ở dòng lệnh tại thư mục chứa file Vagrant để kết nối đến máy ảo bằng giao thức ssh gõ lệnh sau:
         1. vagrant ssh # ssh -> logout: exit
         2. sudo -i #Bạn sẽ đăng nhập vào máy ảo với tài khoản user có tên là vagrant, từ tài khoản này nếu muốn chuyển sang root
    ### Lệnh vagrant hay dùng
    | Lệnh            | Thông tin                                                    |
    | --------------- | ------------------------------------------------------------ |
    | vagrant init    | Sinh file cấu hình máy ảo mới Vagrantfile                    |
    | vagrant up      | Thực hiện tạo / hoặc chạy máy ảo với cấu hình từ Vagrantfile |
    | vagrant ssh     | Kết nối ssh vào máy ảo, tài khoản kết nối là vagrant         |
    | vagrant halt    | Dừng máy ảo (shutdown)                                       |
    | vagrant reload  | Khởi động lại máy ảo, có đọc lại cấu hình trong Vagrantfile  |
    | vagrant destroy | Xóa máy ảo                                                   |
    

  ## Nâng cao

    ### Đồng bộ thư mục
    * Mặc định khi chạy máy ảo, nó đã đồng bộ qua lại giữa thư mục chứa file Vagrantfile vào thư mục /vagrant/ của máy ảo. Nếu muốn cấu hình đồng bộ sử dụng config.vm.synced_folder, ví dụ cần đồng bộ thư mục máy host hiện tại . vào thư mục /data/mydata/ của máy ảo
