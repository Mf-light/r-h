1、在 OpenStack 云平台创建名为  project12-jw220101 的项目，并查询项目列表信息。（5分）
2、在 OpenStack 云平台创建名为 clouduser12-jw220101  的用户，该用户属于 project12-jw220101 项目，并设置用户密码为 redhat2012，查询用户列表信息。（5分）
3、将 cirros 镜像上传到 OpenStack 云平台，镜像名为 cloudimage12-jw220101，并查看镜像列表信息。在 /root 和 /opt 目录下，使用镜像文件 cirros-0.5.1-x86_64-disk.img。（5分）
4、在 OpenStack 云平台创建名为 router12-jw220101 路由器，查询路由器信息。（5分）
5、在 OpenStack 云平台创建名为 pri_jw220101 的私有网络，子网名称为  pri-subnet-jw220101，网络地址 10.1.2.0/24，网关 10.1.2.254，并将网络连接到路由器 router12-jw220101（网络类型geneve） 。（15分）
6、在 OpenStack 云平台创建名为 public-jw220101 的共有网络，子网名称为 public-subnet-jw220101， 网络地址 172.31.32..0/24，网关172.31.32.254，DNS 为172.31.32.254并将网络连接到路由器 router12-jw220101 。（可以使用extnet接口映射）（15分）
7、创建安全组规则和密钥，安全组规则名为  secgroup12-jw220101，设置安全组规则，允许 icmp 和 ssh 协议。创建密钥  mykey12-jw220101。（10分）
8、创建 cirros12.tiny 云主机类型（vcpu为1，内存为128m，磁盘1g），使用  cloudimage12-jw220101 镜像，使用 secgroup12-jw220101 安全组， mykey-jw220101 密钥，启动云主机，名称为 cloud12-jw220101，云主机私有网络为 pri_jw220101 。（15分）
9、在 OpenStack 云平台创建浮动 IP 地址，附加给 cloud12-jw220101 云主机。（10分）
10、创建磁盘 disk12-jw220101，大小为1g，附加给云主机 cloud12-jw220101。（5分）
11、创建对象存储容器 container12-jw220101，上传文件（自定义）到容器。（5分）
12、通过 ovn 网络空间，连接云主机 cloud12-jw220101。（5分）



您列出了一系列在OpenStack云平台上的典型操作，下面是对每个操作的简要概述及命令指导。请注意，具体命令可能因OpenStack版本和使用的命令行客户端（如OpenStack客户端或Horizon界面）有所不同，务必根据实际情况调整。

### 1. 创建项目及查询项目列表
- **创建项目**:
  ```bash
  openstack project create project12-jw220101
  ```
- **查询项目列表**:
  ```bash
  openstack project list
  ```

### 2. 创建用户并分配至项目
- **创建用户**:
  ```bash
  openstack user create --password redhat2012 --project project12-jw220101 clouduser12-jw220101
  ```
- **查询用户列表**:
  ```bash
  openstack user list
  ```

### 3. 上传镜像并查看镜像列表
- **上传镜像**（确保镜像文件路径正确）:
  ```bash
  openstack image create --public --disk-format qcow2 --container-format bare --file /path/to/cirros-0.5.1-x86_64-disk.img cloudimage12-jw220101
  ```
- **查看镜像列表**:
  ```bash
  openstack image list
  ```

### 4. 创建路由器并查询路由器信息
- **创建路由器**:
  ```bash
  openstack router create router12-jw220101
  ```
- **查询路由器信息**:
  ```bash
  openstack router show router12-jw220101
  ```

### 5. 创建私有网络及子网并连接路由器
- **创建网络**:
  ```bash
  openstack network create --provider-network-type geneve pri_jw220101
  ```
- **创建子网**:
  ```bash
  openstack subnet create --network pri_jw220101 --subnet-range 10.1.2.0/24 --gateway 10.1.2.254 pri-subnet-jw220101
  ```
- **连接到路由器**:
  ```bash
  router_id=$(openstack router show router12-jw220101 -f value -c id)
  subnet_id=$(openstack subnet show pri-subnet-jw220101 -f value -c id)
  openstack router add port $router_id --subnet $subnet_id
  ```

### 6. 创建公共网络及子网并连接路由器
- **创建网络**:
  ```bash
  openstack network create --provider-network extnet public-jw220101
  ```
- **创建子网**:
  ```bash
  openstack subnet create --network public-jw220101 --subnet-range 172.31.32.0/24 --gateway 172.31.32.254 public-subnet-jw220101
  ```
- **连接到路由器**同上。

### 7. 创建安全组和密钥
- **创建安全组**:
  ```bash
  openstack security group create secgroup12-jw220101
  ```
- **添加规则**:
  ```bash
  openstack security group rule create --protocol icmp --ethertype IPv4 --group secgroup12-jw220101
  openstack security group rule create --protocol tcp --dst-port 22:22 --ethertype IPv4 --group secgroup12-jw220101
  ```
- **创建密钥**:
  ```bash
  openstack keypair create mykey12-jw220101
  ```

### 8. 创建云主机
- **创建云主机**:
  ```bash
  openstack server create --flavor m1.nano --image cloudimage12-jw220101 --security-group secgroup12-jw220101 --key-name mykey12-jw220101 --network pri_jw220101 cloud12-jw220101
  ```

### 9. 创建浮动IP并附加
- **创建浮动IP**:
  ```bash
  net_id=$(openstack network show public-jw20101 -f value -c id)
  openstack floating ip create $net_id
  ```
- **附加到云主机**:
  ```bash
  floating_ip_id=$(openstack floating ip list --status DOWN -f value -c ID)
  openstack server add floating ip cloud12-jw220101 $floating_ip_id
  ```

### 10. 创建磁盘并附加
- **创建磁盘**:
  ```bash
  openstack volume create --size 1 disk12-jw220101
  ```
- **附加磁盘**:
  ```bash
  vol_id=$(openstack volume show disk12-jw220101 -f value -c id)
  openstack server add volume cloud12-jw220101 $vol_id
  ```

### 11. 创建对象存储容器及上传文件
- **创建容器**:
  ```bash
  swift post container12-jw220101
  ```
- **上传文件**:
  ```bash
  swift upload container12-jw220101 yourfile.txt
  ```

### 12. 通过 OVN 连接云主机
OVN集成通常在OpenStack的网络层，直接操作较少直接通过OVN命令进行。确保云主机网络配置正确即可。具体OVN操作需依据集成程度和需求，可能涉及网络策略调整，通常不直接通过OVN命令直接连接云主机。

每个操作都基于OpenStack的命令行工具执行，确保有相应权限和环境配置。对于复杂的操作，如网络配置，建议先在OpenStack文档中查找最佳实践和特定版本的指导。