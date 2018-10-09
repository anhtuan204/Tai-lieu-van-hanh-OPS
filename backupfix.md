# Mục đích tài liệu
Tài liệu cung cấp hướng dẫn patch bản cập nhật rbd.py nhằm cho phép OpenStack có
thể backup incremential trên Ceph.
- Mặc định đối với bản OpenStack Queens chưa support hoàn toàn việc Backup
Volume theo kiểu incremential đối với Backend là Ceph.
o Mỗi bản Backup là 1 volume trong pool Backups
o Volume này có dung lượng bằng chính dung lượng raw của VM. (VM 20G
thì mỗi bản backup sẽ có dung lượng 20G)

- Bản patch hỗ trợ
o Volume mỗi khi backup sẽ chỉ có 1 volume.base ở trong pool Backups của
Ceph.
o Các bản Increment là các bản snapshot của Volume.base này
o Dung lượng của các bản backup là dung lượng thay đổi của Volume.
o Tổng dung lượng của các bản backup chính bằng dung lượng thực tế của
Volume tại thời điểm backup cuối cùng.

# Hướng dẫn patch bản vá để Openstack backup RBD increment trên
Ceph
Chú ý :
- Thực hiện trên tất cả các Node Controller(Cinder)
- Chỉ thực hiện 1 lần duy nhất
```
mkdir -p /root/nhanhoa-scripts/rbd_new.py
cd /root/nhanhoa-scripts/rbd_new.py
wget https://raw.githubusercontent.com/uncelvel/tutorial-
ceph/master/scripts/rbd_new.py/rbd.py -O rbd.py
mv /usr/lib/python2.7/site-packages/cinder/volume/drivers/rbd.py
/root/nhanhoa-scripts/rbd_new.py/rbd.py.bk
mv rbd.py /usr/lib/python2.7/site-
packages/cinder/volume/drivers/rbd.py /root/nhanhoa-
scripts/rbd_new.py/rbd.py
```
3
```
chmod 644 /usr/lib/python2.7/site-
packages/cinder/volume/drivers/rbd.py
systemctl restart openstack-cinder*
```