# Hướng dẫn tạo volume non-bootable (volume trống) và attach vào VM
-	Kiểm tra vm cần thêm volume đang có bao nhiêu volume gắn vào :

 ![](https://i.imgur.com/EP4D8mT.png)

-	Kiểm tra cấu hình phân bố disk bên trong vm. VM không có phân vùng SWAP. Mục tiêu là sẽ tạo 2 volume, 1 volume chứa DATA và 1 volume chứa phân vùng SWAP :
 
![](https://i.imgur.com/ix0h3h7.png)

-	Trên project của vm đó, tạo volume mới :
 ![](https://i.imgur.com/nK9o6pL.png)

-	Tạo 1 volume DATA chứa dữ liệu. Điền Volume Name, Size, sau đó ấn Create Volume :
 
![](https://i.imgur.com/t7nNFMN.png)
 

-	Sau khi tạo xong, volume cần có trạng thái Available :
 
![](https://i.imgur.com/zW0RjxB.png)
-	Thực hiện Attach volume vào máy ảo :
![](https://i.imgur.com/QnYsAui.png)
 

-	Chọn tên vm và ấn Attach Volume:
![](https://i.imgur.com/621hU5K.png)
 

-	Login vào vm , kiểm tra disk, đã thấy ổ mới là vdb:
![](https://i.imgur.com/VOB43Yx.png)
-	
 

2.	Tạo thư mục và mount với volume mới
-	Ta sẽ tạo 1 partition mới từ ổ cứng vdb (tương ứng với volume DATA đã attach)
o	Bước 1 : dùng câu lệnh fdisk /dev/vdb để tạo 1 partition mới 
o	Bước 2 : ấn n để new 1 partition
o	Bước 3 : Ấn p để chọn định dạng cho partition
o	Bước 4 : Ấn 1 để chọn số cho partition mới tạo
o	Bước 5.1 : Ấn 1 để nhập số cylinder (hoặc ấn ENTER)
o	Bước 5.2 : Chọn size muốn cấp cho phân vùng đó (VD ở đây là cấp 100GB) : +100G
o	Bước 6 : Ấn w để lưu lại và thoát

 
-	Phân vùng /dev/vdb1 được tạo với size là 100GB :
 
-	Định dạng EXT4 cho phân vùng mới : 
mkfs -t /dev/vdb1

-	Tạo thư mục /backup và mount vào phân vùng mới :
```
mkdir /backup
```
-	Mount cứng thư mục với partition vdb1  trong file fstab : 
```
vi /etc/fstab 
/dev/vdb			/backup		ext4		defaults	0 0 
```
-	Mount mềm thư mục /backup :
```
mount /dev/vdb1 /backup
```
-	Sau khi xong, kiểm tra lại thư mục đã mount :
 














