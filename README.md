# PART 1 - EXTEND EBS VOLUME WITHOUT PARTITIONING
​
1. launch an instance from aws console
​
2. check volumes which volumes attached to instance. 
​
3. only root volume should be listed
​
```bash
lsblk
df -h
```
4. create a new volume in the same AZ with the instance from aws console (5 GB for this demo).
​
5. attach the new volume from aws console, then list block storages again.
​
6. root volume and secondary volume should be listed
​
lsblk
df -h
​
7. Check if the attached volume is already formatted or not and has data on it.
```bash
sudo file -s /dev/xvdf
```
8. if not formatted, format the new volume
​
```bash
sudo mkfs -t ext4 /dev/xvdf
```
9. check the format of the volume again after formatting
```bash
sudo file -s /dev/xvdf
```
10.  create a mounting point path for new volume
```bash
sudo mkdir /mnt/mp1
```
11. mount the new volume to the mounting point path
​
```bash
sudo mount /dev/xvdf /mnt/mp1/
```
12.  check if the attached volume is mounted to the mounting point path
​
```bash
lsblk
```
13. Show the available space, on the mounting point path
​
```bash
df -h
```
14. Check if there is data on it or not.
```bash
ls -lh /mnt/mp1/
```
15. if there is no data on it, create a new file to show persistence in later steps
​
```bash
cd /mnt/mp1
sudo touch hello.txt
ls
```
16. Modify the new volume in aws console, and enlarge capacity to double gb (from 5GB to 8GB for this demo).
​
17.  check if the attached volume is showing the new capacity
​
```bash
lsblk
``` 
18. show the real capacity used currently at mounting path, old capacity should be shown.
​
```bash
df -h
```
19. resize the file system on the new volume to cover all available space.
​
```bash
sudo resize2fs /dev/xvdf
```
21.  show the real capacity used currently at mounting path, new capacity should reflect the modified volume size.
​
```bash
df -h
```
22. show that the data still persists on the newly enlarged volume.
​
```bash
ls -lh /mnt/mp1/
```
23. show that mounting point path will be gone when instance rebooted 
```bash
sudo reboot now
```
24. show the new volume is still attached, but not mounted
​
```bash
lsblk
```
25. check if the attached volume is already formatted or not and has data on it.
```bash
sudo file -s /dev/xvdf
```
26. mount the new volume to the mounting point path
```bash
sudo mount /dev/xvdf /mnt/mp1/
```
27. show the used and available capacity is same as the disk size.
​
```bash
lsblk
df -h
```
28. if there is data on it, check if the data still persists.
```bash
ls -lh /mnt/mp1/
```
​
# PART 2 - EXTEND EBS VOLUME WITH PARTITIONING
​
# https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/recognize-expanded-volume-linux.html
# https://www.tecmint.com/fdisk-commands-to-manage-linux-disk-partitions/
​
29. list volumes to show current status, primary (root) and secondary volumes should be listed
​
```bash
lsblk
```
30. show the used and available capacities related with volumes
```bash
df -h
```
31. Create tertiary volume (5 GB for this demo) in the same AZ with the instance on aws console
​
32. Attach the new volume from aws console, then list volumes again.
​
33. Primary (root), secondary and tertiary volumes should be listed
```bash
lsblk
```
34. show the used and available capacities related with volumes
​
```bash
df -h
```
​
35. show the current partitions... use "fdisk -l /dev/xvda" for specific partition
```bash
sudo fdisk -l
```
36. check all available fdisk commands and using "m".
​
```bash
sudo fdisk /dev/xvdg
```
# n -> add new partition (with 1G size)
# p -> primary
# Partition number: 1
# First sector: default - use Enter to select default
# Last sector: +1g   (you can also type sector)
# n -> add new partition (with rest of the size-1G)
# p -> primary
# Partition number: 2
# First sector: default - use Enter to select default
# Last sector: default - use Enter to select default
# w -> write partition table
​
37. Show new partitions
​
```bash
lsblk
```
38. format the new partitions
​
```bash
sudo mkfs -t ext4 /dev/xvdg1
sudo mkfs -t ext4 /dev/xvdg2
```
​
39. create a mounting point path for new volume
​
```bash
sudo mkdir /mnt/mp2
sudo mkdir /mnt/mp3
```
40. mount the new volume to the mounting point path
```
sudo mount /dev/xvdg1 /mnt/mp2/
sudo mount /dev/xvdg2 /mnt/mp3/
```
41. list volumes to show current status, all volumes and partittions should be listed
```bash
lsblk
```
42. show the used and available capacities related with volumes and partitions
```bash
df -h
```
43. if there is no data on it, create a new file to show persistence in later steps
```bash
sudo touch /mnt/mp3/hello.txt
ls  /mnt/mp3/
```
## Enlarge Capacity
​
44. modify the new (3rd) volume in aws console, and enlarge capacity 1 GB more (from 5GB to 6GB for this demo).
​
45. check if the attached volume is showing the new capacity
​
```bash
lsblk
```
46.  show the real capacity used currently at mounting path, old capacity should be shown.
​
df -h
​
47. extend the partition 2 and occupy all newly avaiable space
​
sudo growpart /dev/xvdg 2
​
48. ​show the real capacity used currently at mounting path, updated capacity should be shown.
​
lsblk
​
49. resize and extend the file system
​
sudo resize2fs /dev/xvdg2
​
50. show the newly created file to show persistence
​
ls /mnt/mp3/
​
51. reboot and show that configuration is gone
​
sudo reboot now
​
# PART 3 - AUTOMOUNT EBS VOLUMES AND PARTITIONS ON REBOOT
​
52. back up the /etc/fstab file.
​
sudo cp /etc/fstab /etc/fstab.bak
​
53. open /etc/fstab file and add the following info to the existing.(UUID's can also be used)
​
# /dev/xvdf       /mnt/mp1         ext4  defaults,nofail        0       0
# /dev/xvdg1      /mnt/mp2         ext4  defaults,nofail        0       0
# /dev/xvdg2      /mnt/mp3         ext4  defaults,nofail        0       0
​
sudo nano /etc/fstab  # sudo vim /etc/fstab   >>> for vim
​
# more info for fstab >> https://wiki.debian.org/fstab
​
# reboot and show that configuration exists (NOTE)
​
sudo reboot now
​
# list volumes to show current status, all volumes and partittions should be listed
lsblk
# show the used and available capacities related with volumes and partitions
df -h
# if there is data on it, check if the data still persists.
ls -lh /mnt/mp1/
ls -lh /mnt/mp3/
​
​
# NOTE: You can use "sudo mount -a" to mount volumes and partitions after editing fstab file without rebooting.
##############################
​
​
### Advanced Topic to Try ###
​
In case you deleted private key on your local disk but have it still in the EC2 instance and provided that the volume persists after the instance terminated, you can launch another instance. This time with another key pair, attach the old volume to that instance, ssh to instance, mount the old volume then access to the .ssh folder under home/ec2-user. When mounting, an error pops, to avoid the error use:
# mount -t xfs -o nouuid /dev/xvdf1 /mnt/mp1/
to mount the old volume.
