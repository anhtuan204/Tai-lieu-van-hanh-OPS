# Hướng dẫn tạo thay đổi ip của máy ảo trên OpenStack

Bước 1: Truy cập vào node controller1 thông qua ssh

Bước 2: Truy cập vào thư mục sau để lấy script biến xác thực
```
cd /usr/share/openstack_script/
ls
```
Bước 3:  Chạy câu lệnh sau để chạy biến xác thực project chứa máy ảo, ví dụ ở đây máy ảo đang chạy trên project TTCNTT_2
```
. ttcntt_2-openrc
```
Bước 4: Để lấy danh sách các network đang có, sử dụng câu lệnh sau
```
neutron net-list
```
Bước 5: Để tạo mới port với ip chỉ định, sử dụng câu lệnh sau
```
neutron port-create <net-id> --fixed-ip ip_address=<port-ip>
```
Trong đó:
-	<net-id>: ID của network muốn tạo port (lấy từ câu lệnh bên trên)
-	<port-ip>: Địa chỉ ip muốn tạo

Ví dụ:
```
neutron port-create 9e53cf39-8af3-4eb2-ae2d-f08926df41b2 --fixed-ip ip_address=10.3.11.90
```

Bước 6: Sử dụng câu lệnh sau để lấy danh sách các port, kết hợp với grep ip để filter
```
neutron port-list | grep 10.3.11.90
```
Bước 7: Sử dụng câu lệnh sau để lấy danh sách các máy ảo
```
nova list
```
Bước 8: Sử dụng câu lệnh sau để gán interface mới tạo vào máy ảo
```
nova interface-attach --port-id <port-id> <vm-id>
```
Trong đó:
-	<port-id>: ID của port muốn gán vào máy ảo
-	<vm-id>: ID của máy ảo
Ví dụ: 
```
nova interface-attach --port-id 830791eb-21ec-44a0-92d0-985b45297d6a cf1084be-65ae-49b9-8646-96cf8cf58d6c
```
Bước 9: Detach port cũ của máy ảo
```
nova interface-detach <vm-id> <port-id>
```
Trong đó:
-	<vm-id>: ID của máy ảo
-	<port-id>: ID của port
- Ví dụ: 
```
nova interface-detach cf1084be-65ae-49b9-8646-96cf8cf58d6c bcd62bf5-85ad-4885-a8d3-8497bb7a93e3
```
Bước 10: Console vào máy ảo và set ip tĩnh hoặc reboot lại máy ảo để nhận ip mới.

