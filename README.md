# Chap 1: Docker
* Docker là cái vẹo gì? => la nền tảng để cung cấp cách để building, deploying và running ứng dụng dễ dàng hơn bằng cách sử dụng các containers.
    ## Một số định nghĩa trong Docker
        1. Container: Khi đóng gói một ứng dụng & tất cả các thành phần cần thiết (như một số thư viện) tạo thành 1 gói (package) thì gọi là Container.
        2. Images: là template để tạo ra các container. vd: thường triển khai website lên linux & nginx (hoặc apache) thì có thể xây dựng dựa trên 1 image CentOS & nginx có sẵn để chạy.
        3. Dockerfile: là một file kịch bản dựa vào file kịch bản này Docker sẽ dựa theo để build ra 1 image.
        4. Volumes: là phần dữ liệu được tạo ra khi container được khởi tạo.
    ## Docker architecture
    ![image info](./images/architecture.svg)
    ## Docker basic command
        1. Command build image: docker build -t {name_image} .
        2. Command run image: docker run -d -p {port binding - 5000:80} -- name {name_container} {name_image}
    ## Docker Swarm va Kubernetes (k8s)
    * Khi nói về Docker không thể không đề cập tới **container** đầu tiên như tiêu chuẩn mở để đóng gói & phân phối các ứng dụng container thì đã nãy sinh ra một vấn đề mới. Làm thế nào để tất cả containers được điều phối và lên lịch? làm thể nào để nâng cấp một cách liền mạch mà không bị gián đoạn dịch vụ? Làm cách nào để tracking status của ứng dụng, biết khi nào có sự cố và khởi động lại ứng dụng đó một cách kịp thời?  
        ![image info](./images/2.png)    
    * Lúc này hệ thống điều phối là cần thiết.
       * Docker Swarm: cung cấp cách quản lý một số lượng lớn các container trải rộng trên các cụm máy chủ do Docker tao ra.
       * Kubernetes: là bộ điều phối container được phát triển tại Google, đã được tài trợ cho CNCF và hiện là mã nguồn mở.
    * **Tổng kết:** Kubernetes là một hệ thống toàn diện để tự động hóa việc triển khai, lập lịch và mở rộng quy mô của các ứng dụng được container hóa. Hiện tại, Kubernetes là công ty dẫn đầu thị trường và là tiêu chuẩn cho các bộ điều phối container và deploy các ứng dụng phân tán. Kubernetes có thể chạy được trên các dịch vụ đám mây hoặc tại chỗ (on-premise), có tính mô-đun cao, mã nguồn mở và có một cộng đồng sôi động.
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
  