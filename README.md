# iothread-vq-mapping
This patch enables advanced iothread-vq-mapping for virtio-blk devices in Proxmox VE 9.0. 

Iothread-vq-mapping was introduced in Qemu 9/10.

Read more: https://blogs.oracle.com/linux/post/virtioblk-using-iothread-vq-mapping


Installation:


git clone https://github.com/dominiaz/iothread-vq-mapping

cd iothread-vq-mapping

cp /usr/share/perl5/PVE/QemuServer/Drive.pm /usr/share/perl5/PVE/QemuServer/Drive.pm.backup

cp /usr/share/perl5/PVE/QemuServer.pm /usr/share/perl5/PVE/QemuServer.pm.backup


PVE 9.0

patch /usr/share/perl5/PVE/QemuServer/Drive.pm < drive-iothread-vq-pve9.0.patch

patch /usr/share/perl5/PVE/QemuServer.pm < qemuserver-iothread-vq-pve9.0.patch



systemctl restart pvedaemon

systemctl restart pveproxy

Usage:

Just add iothread_vq_mapping=X (X=2-16) to your drive configuration by editing vm guest configuration file (eg. file in /etc/pve/qemu-server/100.conf):

virtio0: local-nvme:vm-100-disk-0,aio=native,cache=directsync,discard=on,iothread_vq_mapping=8,size=32G

Compatibility:

1) RAW drive image only. QCOW2 is now unsupported by QEMU9.
3) LVM/LVM-Thin for Thin-provisioning and Snapshot comptaibility.

Test results:

fio 4k randread with old official iothread=1: 200k IOPS on VM Guest.

fio 4k randread with iothread_vq_mapping=8: 800k IOPS on VM Guest.

Clone/Backup/Snapshot works with new iothread_vq_mapping parametr.
