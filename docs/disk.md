## 磁盘磁盘

### 识别磁盘设备

查看设备中所能够挂载的磁盘设备

```bash
fdisk -l
```

### 创建文件系统

创建文件系统（如 ext4， xfs等），通用命令 `
mkfs -t ext4 /dev/nvme0n1`

```bash
sudo mkfs.ext4 /dev/nvme0n1
```

### 磁盘挂载与卸载

挂载磁盘前，需要先创建一个挂载点

```bash
mkdir /data
```

临时挂载磁盘

```bash
mount /dev/nvme0n1 /data
```

检查该设备是否已被挂载到系统中

```bash
mount | grep /dev/nvme0n1
```

卸载磁盘

```bash
umount /dev/nvme0n1
```

如果需要永久挂载，需要在 `/etc/fstab` 文件中添加挂载信息

```bash
/dev/nvme0n1 /data ext4 defaults 0 2
```

测试挂载

```bash
mount -a
```
