# Docker
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
    
# Vagrant
   * Vagrant là cái vẹo gì? => Là công cụ tạo ra các máy ảo y chang hyper-v, VMWare hoặc VirtualBox
    ## Setup on Centos 7
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
   