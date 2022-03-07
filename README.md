# 推理部分

## EC2机型选择
实例类型： g4dn.xlarge<br/>
OS： ubuntu/images/hvm-ssd/ubuntu-focal-20.04-amd64-server-20211129<br/>
AMI： ami-0892d3c7ee96c0bf7<br/>
系统盘： 300G GP3<br/>

## 安装部署
1. 更新apt索引
```
apt-get update
```
2. 安装lrzsz,zip
```
apt install lrzsz zip -y
```
3. 上传代码<br/>
使用rz命令将附件中代码docker.zip上传到/root目录并解压

4. 安装docker
```
curl https://get.docker.com | sh  && sudo systemctl --now enable docker
```
5. 安装nvidia-docker2
```
distribution=$(. /etc/os-release;echo $ID$VERSION_ID) \
   && curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add - \
   && curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
apt-get update<br/>
apt-get install -y nvidia-docker2 –y<br/>
systemctl restart docker<br/>
```
6. 测试验证
```
docker run --rm --gpus all nvidia/cuda:11.0-base nvidia-smi
```
7. 安装docker-compose
```
curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
```
8. 安装驱动
```
apt install nvidia-driver-470 –y
```
9. 重启
```
reboot
```
10. 启动推理服务
```
docker-compose -f docker-compose-single-gpu.yaml up -d \
--scale tianlongintent_sementic_web-1=4 \
--force-recreate --remove-orphans
```

# 训练部分
## EC2机型选择
实例类型： g5.xlarge<br/>
OS： Deep Learning AMI (Ubuntu 18.04) Version 56.1<br/>
AMI： ami-001d9a2fbb03228ee<br/>
系统盘： 300G GP3<br/>

## 安装部署
1. 更新apt索引
```
apt-get update
```
2. 安装lrzsz,zip
```
apt install lrzsz zip -y
```
3. 上传代码<br/>
使用rz命令将附件中代码上传到/root目录并解压

4. 修改配置
```
vim config/bert_config.json
"bert_model_path": "bert-large-uncased",
```
5. 安装驱动
```
apt install nvidia-driver-510 –y
```
6. 安装依赖
```
pip3 install –r requirements.txt
```
7. 安装pytorch
```
pip3 install torch==1.10.2+cu113 torchvision==0.11.3+cu113 torchaudio==0.10.2+cu113 -f https://download.pytorch.org/whl/cu113/torch_stable.html
```
8. 重启
```
reboot
```
9. 执行训练
```
python3 train_gpu.py
```