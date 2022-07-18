## WEB SOLUTION WITH WORDPRESS
### In this project I I'm tasked to prepare storage infrastructure on two Linux servers and implement a basic web solution using WordPress. WordPress is a free and open-source content management system written in PHP and paired with MySQL or MariaDB as its backend Relational Database Management System (RDBMS).

## Project 6 consists of two parts:

#### Configure storage subsystem for Web and Database servers based on Linux OS. The focus of this part is to give me a practical experience of working with disks, partitions and volumes in Linux.

#### I will Install WordPress and connect it to a remote MySQL database server. This part of the project will solidify my skills of deploying Web and DB tiers of Web solution.

### I will be using RedHat Linux server for this Project

### 1. I will LAUNCH AN EC2 INSTANCE THAT WILL SERVE AS “WEB SERVER

![webserver](https://user-images.githubusercontent.com/19933457/179570407-6b1dc39d-10d7-4c72-a551-60890f53d20e.png)

### 2. I created 3 volume and Attach all three volumes one by one to my Web Server EC2 instance

![ScreenShot_07_07_2022_10_19_38](https://user-images.githubusercontent.com/19933457/179570896-00062b98-bf86-4325-b848-be1b312070e9.png)

### 3. I Use the command below to inspect what block devices are attached to the server
`lsblk`

![ScreenShot_07_07_2022_11_15_34](https://user-images.githubusercontent.com/19933457/179571236-6de04417-c19f-4ce8-a85b-05934dba289e.png)

### 4. I Use the command below to see all mounts and free space on my server
`df -h`
![ScreenShot_07_07_2022_11_16_38](https://user-images.githubusercontent.com/19933457/179571655-c95e0fd6-8a08-4dc0-9dac-085873d403bf.png)

### 5. I Use the comand below to create a single partition on each of the 3 disks
`gdisk`
`sudo gdisk /dev/xvdf`
![ScreenShot_07_07_2022_11_24_39](https://user-images.githubusercontent.com/19933457/179572840-fd748221-f9c1-4a61-8922-11b5705b6b9e.png)

`sudo gdisk /dev/xvdh`
![ScreenShot_07_07_2022_11_25_29](https://user-images.githubusercontent.com/19933457/179572849-8aef401a-b349-46a5-bc6b-d62b4a47e4a1.png)

`sudo gdisk /dev/xvdg`
![ScreenShot_07_07_2022_11_26_23](https://user-images.githubusercontent.com/19933457/179572852-3a5340ae-450f-40ed-8459-f800d1307c20.png)

### 6. Use the command utility below to view the newly configured partition on each of the 3 disks.
`lsblk`
![ScreenShot_07_07_2022_11_31_51](https://user-images.githubusercontent.com/19933457/179573516-45398f1b-135c-4afc-9b32-5e1e602be3af.png)

### 7. I Install lvm2 package using the below command. 
`sudo yum install lvm2`
![ScreenShot_07_07_2022_11_38_16](https://user-images.githubusercontent.com/19933457/179573955-22b13fdf-c2a9-4f5a-aceb-3a66407598cf.png)

### I also Run sudo lvmdiskscan command to check for available partitions.
`sudo lvmdiskscan`
![ScreenShot_07_07_2022_11_38_55](https://user-images.githubusercontent.com/19933457/179573976-bd4f06b0-c440-4ecc-a59f-ae0059d3a267.png)

### 8. Use pvcreate utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM
`sudo pvcreate /dev/xvdf1 /dev/xvdg1 /dev/xvdh1`

![ScreenShot_07_07_2022_11_41_18](https://user-images.githubusercontent.com/19933457/179574377-1b37b9db-e426-4444-ab36-f5a63d5d5625.png)

### 9.Verify that your Physical volume has been created successfully by running sudo pvs
`sudo pvs`
![ScreenShot_07_07_2022_11_41_42](https://user-images.githubusercontent.com/19933457/179574967-ff0fd996-6884-4702-91bb-b115bbd05c7e.png)

### 9. I Use vgcreate utility to add all 3 PVs to a volume group (VG). Name the VG webdata-vg

`sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1`

![ScreenShot_07_07_2022_11_43_32](https://user-images.githubusercontent.com/19933457/179575156-033749e5-d8bb-4b67-b999-bdba75ce63bb.png)

### 10. I Verify that my VG has been created successfully by running below command
`sudo vgs`
![ScreenShot_07_07_2022_11_44_12](https://user-images.githubusercontent.com/19933457/179575480-b03be8d1-b3ca-49a1-83dc-cd36fd85ec9d.png)

### 11. I Use lvcreate utility to create 2 logical volumes. apps-lv (Use half of the PV size), and logs-lv Use the remaining space of the PV size. NOTE: apps-lv will be used to store data for the Website while, logs-lv will be used to store data for logs.

`sudo lvcreate -n apps-lv -L 14G webdata-vg`

![ScreenShot_07_07_2022_11_47_37](https://user-images.githubusercontent.com/19933457/179575934-8b1850b4-1f75-460b-a6a3-de27226ee1be.png)

`sudo lvcreate -n logs-lv -L 14G webdata-vg`

![ScreenShot_07_07_2022_11_47_58](https://user-images.githubusercontent.com/19933457/179575931-fc344a20-5008-4b85-b995-b0a4f327067f.png)

###  12. I Verify that your Logical Volume has been created successfully by running sudo lvs
`sudo lvs`

![ScreenShot_07_07_2022_11_48_20](https://user-images.githubusercontent.com/19933457/179576353-e380d74b-3a17-4f53-814b-a2ce1db4616b.png)

### 13. I Verify the entire setup
`sudo vgdisplay -v #view complete setup - VG, PV, and LV`

![ScreenShot_07_07_2022_11_49_37](https://user-images.githubusercontent.com/19933457/179576659-5bc7dafe-1f24-4397-a2eb-603fa1854fe6.png)

`sudo lsblk`

![ScreenShot_07_07_2022_11_50_03](https://user-images.githubusercontent.com/19933457/179576676-c1bff43e-2015-497f-af62-ea3b3a50776b.png)

### 14. I Use mkfs.ext4 to format the logical volumes with ext4 filesystem

`sudo mkfs -t ext4 /dev/webdata-vg/apps-lv`

![ScreenShot_07_07_2022_11_51_33](https://user-images.githubusercontent.com/19933457/179577067-7c2e42b8-9509-4ac2-9eac-19ce96c6df1c.png)

`sudo mkfs -t ext4 /dev/webdata-vg/logs-lv`

![ScreenShot_07_07_2022_11_52_00](https://user-images.githubusercontent.com/19933457/179577079-e3981a75-7086-4e93-b9e7-35f1c69edc75.png)

### 15. Create /var/www/html directory to store website files
`sudo mkdir -p /var/www/html`
![ScreenShot_07_07_2022_11_55_57](https://user-images.githubusercontent.com/19933457/179577582-b6afbb4a-b0ee-4787-97d9-e1e604250822.png)

### 16. Create /home/recovery/logs to store backup of log data
`sudo mkdir -p /home/recovery/logs`
![ScreenShot_07_07_2022_11_56_15](https://user-images.githubusercontent.com/19933457/179577593-ed224be5-d640-4a9a-9716-d516488bb1a5.png)

### 17. Mount /var/www/html on apps-lv logical volume
`sudo mount /dev/webdata-vg/apps-lv /var/www/html/`
![ScreenShot_07_07_2022_12_28_51](https://user-images.githubusercontent.com/19933457/179577879-a53f46c6-d7db-4505-bf94-407def9715a1.png)

### 18. Use rsync utility to backup all the files in the log directory /var/log into /home/recovery/logs (This is required before mounting the file system)
`sudo rsync -av /var/log/. /home/recovery/logs/`

![ScreenShot_07_07_2022_12_33_57](https://user-images.githubusercontent.com/19933457/179578721-2caa1dc4-a926-4656-a9c8-c811c0ba95ed.png)

### 19. Mount /var/log on logs-lv logical volume. (Note that all the existing data on /var/log will be deleted)
`sudo mount /dev/webdata-vg/logs-lv /var/log`

![ScreenShot_07_07_2022_12_39_12](https://user-images.githubusercontent.com/19933457/179578738-c90ab096-8c20-4f4d-b51a-93e2af394d77.png)

### 20. Restore log files back into /var/log directory

`sudo rsync -av /home/recovery/logs/. /var/log`

### 21. I UPDATE THE `/ETC/FSTAB` FILE The UUID of the device will be used to update the /etc/fstab file;
`sudo blkid`
![ScreenShot_07_07_2022_12_43_25](https://user-images.githubusercontent.com/19933457/179579102-530fedf6-6e55-4823-93a9-51806a4ebc01.png)

### 22. Update /etc/fstab in this format using your own UUID and rememeber to remove the leading and ending quotes.
`sudo vi /etc/fstab`
![ScreenShot_07_07_2022_12_48_34](https://user-images.githubusercontent.com/19933457/179579655-0edfaf38-cb48-48ec-8ffb-aee41049f32c.png)

### 23. Test the configuration and reload the daemon
`sudo mount -a`
![ScreenShot_07_07_2022_12_55_38](https://user-images.githubusercontent.com/19933457/179579899-3c82bcfa-217f-41eb-84ea-b02e66ef23f1.png)

`sudo systemctl daemon-reload`
![ScreenShot_07_07_2022_12_56_12](https://user-images.githubusercontent.com/19933457/179579919-c975c604-0206-49d0-89e4-68075f68960e.png)

### 24 I Verify your setup by running df -h.

## Step 2 — Prepare the Database Server

### 25 I Launch a second RedHat EC2 instance that will have a role – ‘DB Server’
### I Repeat the same steps as for the Web Server, but instead of apps-lv I create db-lv and mount it to /db directory instead of /var/www/html/.

![ScreenShot_07_07_2022_13_12_07](https://user-images.githubusercontent.com/19933457/179579948-25573482-99db-4df4-82c8-840e0f24dcb6.png)

### 24 I Verify your setup by running df -h.
![ScreenShot_07_07_2022_13_40_58](https://user-images.githubusercontent.com/19933457/179581295-44010d41-2a18-423e-8d92-c8f292d9c9ee.png)

## Step 3 — I Install WordPress on my Web Server EC2

### 1. I Update the repository
`sudo yum -y update`
![ScreenShot_07_07_2022_14_13_37](https://user-images.githubusercontent.com/19933457/179582450-d57b785a-57cd-4737-b452-12d4b483bad4.png)

### 2. Install wget, Apache and it’s dependencies
`sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json`
![ScreenShot_07_07_2022_14_13_47](https://user-images.githubusercontent.com/19933457/179582491-b32a9fa3-2921-4770-9996-38036db56150.png)
![ScreenShot_07_07_2022_14_20_43](https://user-images.githubusercontent.com/19933457/179582536-33db1e41-58dd-466b-8daa-13fab04f38db.png)

### 3.  Start Apache
`sudo systemctl enable httpd`
`sudo systemctl start httpd`

### 4. To install PHP and it’s depemdencies, I will run the following command
`sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm`


![ScreenShot_07_07_2022_14_29_03](https://user-images.githubusercontent.com/19933457/179586712-d2f87208-005b-43ec-9bd6-3e36bc5ac6ce.png)

`sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm`

![ScreenShot_07_07_2022_14_29_28](https://user-images.githubusercontent.com/19933457/179587887-3dbb9395-f029-4f2f-a5a1-ed406442c55b.png)

`sudo yum module list php`

![ScreenShot_07_07_2022_14_30_17](https://user-images.githubusercontent.com/19933457/179588809-25f398b0-76cb-4f25-bdc6-36a23a03a5a6.png)

`sudo yum module reset php`
![ScreenShot_07_07_2022_14_32_00](https://user-images.githubusercontent.com/19933457/179589947-cdcba372-5f26-490b-baf0-1105f5be6e3c.png)

`sudo yum module enable php:remi-7.4`
![ScreenShot_07_07_2022_14_32_42](https://user-images.githubusercontent.com/19933457/179590284-61b7b0c1-27f4-4ca2-88a3-097a0afe4a6e.png)

`sudo yum install php php-opcache php-gd php-curl php-mysqlnd`
![ScreenShot_07_07_2022_14_33_55](https://user-images.githubusercontent.com/19933457/179592018-e38dbac5-3136-43f5-ac3d-f62fca57b874.png)

`sudo systemctl start php-fpm`
![ScreenShot_07_07_2022_14_35_18](https://user-images.githubusercontent.com/19933457/179592225-f01e6c58-34a6-4045-8ecb-9133fc17e83a.png)

`sudo systemctl enable php-fpm`
![ScreenShot_07_07_2022_14_36_04](https://user-images.githubusercontent.com/19933457/179592434-a5c50d43-258a-4af4-9bcd-ed115270936c.png)
![ScreenShot_07_07_2022_14_36_54](https://user-images.githubusercontent.com/19933457/179592638-af2501cc-7c4c-4fff-863b-3a74c231fcb1.png)

`setsebool -P httpd_execmem 1`

![ScreenShot_07_07_2022_14_38_34](https://user-images.githubusercontent.com/19933457/179596141-21366adb-0b10-4945-a775-eacadddda8fe.png)

### 5. Restart Apache
`sudo systemctl restart httpd`
![ScreenShot_07_07_2022_14_41_46](https://user-images.githubusercontent.com/19933457/179598986-cc57bb06-246a-4678-bf5a-32b46f5829ee.png)

`sudo systemctl status httpd`
![ScreenShot_07_07_2022_14_42_16](https://user-images.githubusercontent.com/19933457/179599022-817f798b-d24f-452c-a0d4-542aac941b64.png)

### 6. I will Download wordpress and copy wordpress to var/www/html
`mkdir wordpress cd   wordpress`
![ScreenShot_07_07_2022_14_48_42](https://user-images.githubusercontent.com/19933457/179599737-1a042750-806c-47b0-b9f9-c3052353a0bf.png)

`sudo wget http://wordpress.org/latest.tar.gz`
![ScreenShot_07_07_2022_14_48_42](https://user-images.githubusercontent.com/19933457/179599737-1a042750-806c-47b0-b9f9-c3052353a0bf.png)

`sudo tar xzvf latest.tar.gz`
![ScreenShot_07_07_2022_14_50_57](https://user-images.githubusercontent.com/19933457/179600008-ec489ad1-11ca-488a-9ef9-524fafd9a097.png)

`sudo rm -rf latest.tar.gz`

`cp wordpress/wp-config-sample.php wordpress/wp-config.php`

![ScreenShot_07_07_2022_14_57_40](https://user-images.githubusercontent.com/19933457/179600431-30306bf8-303a-4bd8-a578-160dae115f76.png)

`cp -R wordpress /var/www/html/`

![ScreenShot_07_07_2022_15_02_26](https://user-images.githubusercontent.com/19933457/179600443-9ebd9567-0a34-4ab3-ac7c-8fbe4c9207dc.png)

## Step 4 — Install MySQL on on DB Server EC2
`sudo yum update`
`sudo yum install mysql-server`

### Verify that the service is up and running by using sudo systemctl status mysqld, if it is not running, restart the service and enable it so it will be running even after reboot:
`sudo systemctl restart mysqld`
`sudo systemctl enable mysqld`
![ScreenShot_07_07_2022_15_23_44](https://user-images.githubusercontent.com/19933457/179601640-b0212b43-5ca3-409b-af58-5db13dc2c87e.png)



![ScreenShot_07_07_2022_15_20_18](https://user-images.githubusercontent.com/19933457/179601189-5daa3ebb-cafb-47bb-a902-4e87f7c83d26.png)

## Step 5 — Configure DB to work with WordPress
`sudo mysql`
![ScreenShot_07_07_2022_15_25_07](https://user-images.githubusercontent.com/19933457/179601662-e68dea82-3fa1-41ab-a005-0c1161a4f2eb.png)

`CREATE DATABASE wordpress;`
![ScreenShot_07_07_2022_15_25_53](https://user-images.githubusercontent.com/19933457/179601750-b5dd05ad-94c2-4d9a-bea1-7939cdcff67c.png)

`Show DATABASE;`
![ScreenShot_07_07_2022_15_26_25](https://user-images.githubusercontent.com/19933457/179601784-f8311f5d-51c5-40e9-a5e5-0c7ef0d9db41.png)

`CREATE USER 'wordpress'@'%' IDENTIFIED WITH mysql_native_password BY 'wordpress';`
![ScreenShot_07_07_2022_15_35_54](https://user-images.githubusercontent.com/19933457/179602747-5ede7fd1-c419-4004-b8f4-f23d448497fb.png)

`GRANT ALL PRIVILEGES ON *.* TO 'wordpress'@'%' WITH GRANT OPTION;`
![ScreenShot_07_07_2022_15_36_37](https://user-images.githubusercontent.com/19933457/179602829-150edbe6-2e2a-4b25-839a-12e8d260b1c2.png)

`FLUSH PRIVILEGES;`
![ScreenShot_07_07_2022_15_37_05](https://user-images.githubusercontent.com/19933457/179602898-7c66afb3-d031-49fc-865a-c31a4cd7e034.png)

`SHOW DATABASES;`
![ScreenShot_07_07_2022_15_43_11](https://user-images.githubusercontent.com/19933457/179603153-3843562c-453f-4ac1-b0c2-7d9a1cd69b61.png)

## Step 6 — I Configure WordPress to connect to remote database.
### I open MySQL port 3306 on DB Server EC2. For extra security, I shall allow access to the DB server ONLY from my Web Server’s IP address, so in the Inbound Rule configuration specify source as /32

![ScreenShot_07_07_2022_15_50_01](https://user-images.githubusercontent.com/19933457/179603569-96a9a0d7-ddeb-436d-b383-4879866bd366.png)
![ScreenShot_07_07_2022_16_00_43](https://user-images.githubusercontent.com/19933457/179603577-2a14b476-50d0-49af-8ccf-1c3745d81d2d.png)

### Final Result with my wordpress website page
![ScreenShot_07_07_2022_16_09_40](https://user-images.githubusercontent.com/19933457/179603704-2cb2d228-075a-4bb4-ad0b-d7116617afcc.png)
![ScreenShot_07_07_2022_16_09_52](https://user-images.githubusercontent.com/19933457/179603720-e4b24af1-c0e9-46d1-a998-d248b262be62.png)
![ScreenShot_07_07_2022_16_10_50](https://user-images.githubusercontent.com/19933457/179603726-2a5b6115-3d32-4681-8d73-3b4ec0f2aefd.png)
![ScreenShot_07_07_2022_16_11_52](https://user-images.githubusercontent.com/19933457/179603734-1bafef6e-59dc-4dd9-af07-c6a529b4a172.png)
![ScreenShot_07_07_2022_16_12_51](https://user-images.githubusercontent.com/19933457/179603785-d8980d16-bef9-4db1-bf91-9e10c2deb126.png)
![ScreenShot_07_07_2022_16_13_13](https://user-images.githubusercontent.com/19933457/179603793-032fedea-8626-4c90-970a-513b4a373853.png)
![ScreenShot_07_07_2022_16_13_40](https://user-images.githubusercontent.com/19933457/179603804-e7f5482b-4f94-4fab-94b9-346d8916552f.png)








































