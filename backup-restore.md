# Mục đích tài liệu
Tài liệu cung cấp hướng dẫn Backup và Restore VM trên OpenStack bản Queens với backup là Ceph bản Luminous
1.	Hướng dẫn Backup trên OpenStack 
Chú ý: Đảm bảo các node Controller đã được thực hiện bản patch rbd.py để các volume có thể backup incremential
Bước 1 : Backup Volumes (Thực hiện trên Node Controller)
-	Lấy ID của volume cần backup
```
source admin-openrc
opentack volume list –all-projects | grep <VM_name> | awk ‘{print $2}’
```
-	Backup
```
VOLUME_ID=’<ID>’
openstack volume backup create --name  $VOLUME_ID-date-$(date '+%Y%m%d-%H%M%S') --force  $VOLUME_ID
```
Bước 2 : Kiểm tra bản backup 
# Kiểm tra trên Node Controller
```
openstack volume backup list 
```
# Kiểm tra trên Node Ceph
```
rbd ls backups
```
2.	Hướng dẫn Restore trên OpenStack 
Chú ý:  Restore Volume từ bản backup có 2 cách 
-	Cách 1: Restore vào chính volume của VM ban đầu bằng cách Xóa VM  Restore backup vào volume  Create port với IP cũ  Create lại VM từ Volume, IP cũ. 
-	Cách 2: Tạo volume mới (Volume_new)   Restore vào Volume mới này  Detach IP ra khỏi VM cũ  Create VM từ Volume_new với IP là IP ban đầu của VM
Để quản lý thuận tiện cho việc backup theo Volume_ID thì ở hướng dẫn này chúng ta sử dụng Cách 1*
Bước 1:  Add Admin vào Project của Khách hàng, thiết lập môi trường. 
# Add admin user vào Project của Khách hàng
```
openstack role add --user admin --project <TENANT_ID> user
```
# Switch qua project của Khách hàng để thao tác
```
cp admin-openrc ACCxxxx
sed –i ‘s| export OS_USERNAME=admin| export OS_USERNAME= ACCxxxx|g’ ACCxxxx
source ACCxxxx
```
Bước 2:  Xóa VM 
# Lấy ID của VM
```
openstack server list | grep <VM_name> | awk ‘{print $2}’
```
# Lấy Volume_ID của VM
```
openstack server show <VM_ID> 
```
# Xóa VM 
openstack server delete <VM_ID>	
Bước 3: Kiểm tra bản backup chọn bản backup muốn Restore
# Kiểm tra trên Node Controller
openstack volume backup list | grep <Volume_ID>
Bước 4: Restore Volume từ bản backup 
openstack volume backup restore <Backup_ID> <Volume_ID>
Bước 5: Create port với IP là IP cũ của VM
openstack port create --network <network> --fixed-ip ip-address=<IP> <Description>
# Lấy thông tin ID port 
```
openstack port list | grep <IP> | awk ‘{print $2}’
```
Bước 6: Khởi tạo lại VM từ Volume đã được Restore
```
cat << EOF >> cloudinit.sh 
#cloud-config 
password: nhanhoa2018@ 
chpasswd: { expire: False } 
ssh_pwauth: True
EOF

openstack server create <VM_name> --volume <VOLUME_ID> --flavor CLOUD_SSD_D --nic port-id=<PORT_ID> --availability-zone nova --user-data cloudinit.sh
```
Bước 7: Login và kiểm tra VM 
- ssh root@<IP> 
Bước 8:  Remove admin user ra khỏi Project của Khách hàng 
# Remove admin user vào Project của Khách hàng
```
openstack role del --user admin --project <TENANT_ID> user
```

