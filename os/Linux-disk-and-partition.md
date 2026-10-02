# [study-linux] Linux disk and partition

## Format new disk and create single and full partition

```bash
sudo lsblk  # List block devices and find your empty device.
sudo fdisk /dev/sdb  # Replace to your device, e.g, /dev/sda.

# In the fdisk's interactive interface, press these keys (each followed by `Enter`):
n  # New partition
p  # Primary partition
1  # Partition number (1)
   # Default first sector (press Enter)
   # Default last sector (press Enter)
w  # Write changes and exit

sudo lsblk  # Verify the new partition is created.
sudo mkfs.ext4 /dev/sda1  # Replace to your partition, e.g, /dev/sda1

# Mount the partition for testing.
sudo mkdir /mnt/nas
sudo chmod 777 /mnt
sudo chmod 777 /mnt/nas
sudo mount /dev/sda1 /mnt/nas

# Mount the partition automatically on boot.
sudo blkid /dev/sda1  # The output will be like: /dev/sdb1: UUID="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" TYPE="ext4"
sudo vim /etc/fstab
# Append the following line to /etc/fstab:
UUID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx /mnt/nas      ext4      defaults  0      2
# <device>                                <mount_point> <fs_type> <options> <dump> <pass>

sudo mount -a  # Test the fstab configuration (should not show errors).
sudo reboot  # Reboot to verify automount works.
```
