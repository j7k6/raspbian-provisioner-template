# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "debian/buster64"
  config.vm.synced_folder ".", "/vagrant", disabled: true

  config.vm.provision "shell", inline: <<-SHELL
    export DEBIAN_FRONTEND=noninteractive

    sudo apt-get update
    sudo apt-get install -y unzip qemu-system-arm psmisc ansible sshpass

    cd /tmp
    wget https://raw.githubusercontent.com/dhruvvyas90/qemu-rpi-kernel/master/versatile-pb.dtb
    wget -O qemu-rpi-kernel https://raw.githubusercontent.com/dhruvvyas90/qemu-rpi-kernel/master/kernel-qemu-4.19.50-buster
    wget -O raspbian.zip http://director.downloads.raspberrypi.org/raspbian_lite/images/raspbian_lite-2019-07-12/2019-07-10-raspbian-buster-lite.zip
    unzip raspbian.zip
    mv *-raspbian-*-lite.img raspbian-provisioned.img

    sudo losetup -P /dev/loop0 raspbian-provisioned.img
    sudo mount /dev/loop0p1 /mnt
    sudo touch /mnt/ssh
    sudo umount /mnt
    sudo losetup -d /dev/loop0

    sudo chown vagrant:vagrant raspbian-provisioned.img

    qemu-system-arm -daemonize -vnc :1 \
      -cpu arm1176 -m 256 \
      -kernel qemu-rpi-kernel \
      -M versatilepb -dtb versatile-pb.dtb \
      -no-reboot \
      -append "dwc_otg.lpm_enable=0 root=/dev/sda2 rootfstype=ext4 elevator=deadline fsck.repair=yes rootwait" \
      -drive "file=raspbian-provisioned.img,index=0,media=disk,format=raw" \
      -net user,hostfwd=tcp::5022-:22 -net nic 2>/dev/null
  SHELL
end
