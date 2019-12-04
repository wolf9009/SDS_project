# SDS_project
project for SDS
1. Preliminary Setup
1.1. ต่อบอร์ด raspberry pi กับจอ แล้วเสียบ adapter เพื่อเปิดบอร์ด
1.2. หลัง boot เสร็จแล้ว พิมพ์ใน terminal ว่า sudo nano /etc/dhcpcd.conf แล้ว enter
1.3. เพิ่ม 4 บรรทัดนี้ไว้ที่ท้ายไฟล์โดย x ให้เปลี่ยนเป็นเลข IP หลักสุดท้ายของสมาชิกกลุ่มแต่ละคน

interface eth0
static ip_address=192.168.99.x/24
static routers=192.168.99.1
static domain_name_servers=8.8.8.8
ใส่เสร็จแล้ว ปิดไฟล์ด้วยการกด Ctrl+X, y และ Enter เพื่อบันทึกไฟล์
1.4. พิมพ์ใน terminal ว่า sudo raspi-config แล้ว enter
1.5. กดลูกศรขึ้น-ลงบนคีย์บอร์ดเลือกที่ Change User Password กด Enter แล้วเปลี่ยนรหัสผ่านบอร์ดให้
เรียบร้อย (จํารหัสใหม่ที่ตั้งไว้ด้วย ไม่ควรใช้บอร์ด pi ด้วย default password อาจเกิดปัญหา ssh
เพื่อคุมเครื่อง pi ด้วย default password ได้จากที่อื่น ๆ – security issue)
1.6. กลับมาสู่หน้า raspi-config หลัก เลือก Interfacing Options แล้วเข้าไป enable SSH, VNC โดย
การเลือกหัวข้อแล้วกด Enter > Enter
1.7. ออกมาที่หน้าจอ raspi-config หลัก (กด ESC) เข้าไปที่ Advanced Options เลือก A5 Resolution
แล้วเลือกความละเอียดจอที่ DMT Mode 85 1280x720 60 Hz 16:9 (เป็นความละเอียดที่เหมาะสม
สําหรับการเข้าไปคุมบอร์ดด้วย VNC และต้องตั้งก่อนเข้าใช้งานผ่าน VNC หากไม่ตั้งก่อน VNC จะไม่
สามารถแสดง Desktop ของบอร์ด pi ได้)
1.8. กลับมาที่หน้า config หลัก เข้าไปที่ Network Options
1.8.1. ไปที่ N1 Hostname จากนั้นจึงเปลี่ยนชื่อ hostname ให้ต่างจากบอร์ดอื่น ๆ ที่มีอยู่แล้ว
1.8.2. ไปที่ N2 Wi-fi แล้วใส่ SSID และ password ของ Wi-Fi Hotspot ที่ต้องการให้บอร์ดต่อ
อินเทอร์เน็ตด้วย
1.9. กลับมาที่หน้าหลัก เข้าไปที่ Localisation Options แล้วตั้งค่าต่าง ๆ ดังนี้
1.9.1. Change Timezone ให้ enter เข้าไปตั้งต่า Geographic area เป็น Asia และตั้ง Time zone
เป็น Bangkok
1.9.2. Change Wi-fi Country ให้ตั้งเป็น TH Thailand
1.10. ออกจากหน้า raspi-config (กด ESC ประมาณ 2-3 ครั้ง ให้กลับมาที่ terminal) แล้ว reboot
เครื่องโดยสั่ง sudo-reboot
1.11. หลัง reboot เครื่องเสร็จแล้ว นําสายแลนต่อระหว่างบอร์ด pi กับ notebook ตัวเอง (หรือต่อ
สายแลนจากคอมกับสายแลนจากบอร์ดมาต่อกันที่ router/switch ก็ได้) แล้วใช้ VNC viewer ในเครื่อง
เข้าไปคุม raspberry pi โดยใช้รหัสผ่านของ pi รหัสใหม่ที่ตั้งไว้(อย่าลืมตั้งค่า static IP ที่ Ethernet
Adapter โน้ตบุ๊คตัวเองด้วย โดยตั้งให้อยู่ในวง 192.168.99.X/24 และเลข IP ห้ามซ้ํากัน IP ของ
raspberry pi)
1.12. เมื่อเข้ามาที่ raspberry pi desktop ได้แล้ว ให้ตรวจสอบการตั้งค่าอีกครั้ง โดยกดที่รูป
raspberry เลือก Preference > Raspberry Pi Configuration แล้วดูผลการต่างค่าว่าตรงกับที่แก้มั้ย
ตรวจสอบว่าบอร์ดสามารถเชื่อมต่ออินเทอร์เน็ตผ่าน Wi-Fi Hotspot ที่เราตั้งค่าไว้ได้ กรณีต่อบอร์ด
เข้า router ร่วมกับบอร์ดอื่น ๆ ตรวจสอบด้วยว่า ping ระหว่างบอร์ดได้ทุกคู่หรือไม่
2. Common Setup

ส่วนนี้จะนําขั้นตอนมากจากเว็บไซต์ medium (https://medium.com/nycdev/k8s-on-pi-
9cc14843d43) แต่ว่ามีการปรับแก้คําสั่งบางอย่างด้วย

- ตรวจสอบให้แน่ใจว่าบอร์ดเชื่อมต่ออินเทอร์เน็ต Wi-Fi ได้
- Common Setup เป็นการตั้งค่าทั้งบน Master Node และ Worker Node
2.1. เข้าไปที่หน้า terminal แล้วพิมพ์ command นี้ เพื่อติดตั้ง docker และสร้าง docker user group บน
raspberry pi
curl -sSL get.docker.com | sh && \
sudo usermod [username] -aG docker && \
newgrp docker
2.2. (ขั้นตอนนี้เฉพาะ raspberry pi เท่านั้น ตัว master node ไม่ต้อง เว้นแต่ว่า master node ใช้
Raspbian เหมือนกัน) แก้ไขไฟล์ /boot/cmdline.txt โดยใช้คําสั่ง sudo /boot/cmdline.txt แล้ว
เพิ่มบรรทัดนี้ต่อท้ายบรรทัดที่มีอยู่ในไฟล์เดิม ไม่ต้องขึ้นบรรทัดใหม่
cgroup_enable=cpuset cgroup_memory=1 cgroup_enable=memory
หลังจากแก้เสร็จแล้ว ให้ reboot
2.3. (เฉพาะ raspberry pi node or Raspbian-based node) เมื่อ reboot เสร็จแล้ว ให้เข้าไปที่
terminal แล้วพิมพ์ command นี้ เพื่อปิดการทํางานของ swap ไม่ให้รบกวนการทํางานของ
Kubernetes
sudo dphys-swapfile swapoff && \
sudo dphys-swapfile uninstall && \
sudo update-rc.d dphys-swapfile remove
กรณีที่ node ใช้ OS เป็น ubuntu ให้ปิดการทํางานของ swap โดยเข้าไป comment บรรทัดของ
swap ในไฟล์ชื่อ /etc/fstab ออก (ใช้วิธีเพิ่ม # ไว้หน้าบรรทัด) จากนั้นจึงค่อย reboot ดูรายละเอียด
เพิ่มเติมที่https://askubuntu.com/questions/214805/how-do-i-disable-swap คําตอบที่ 2
หมายเหตุ: การปิด swap ของ raspberry pi ด้วยคําสั่งข้างต้นจะมีผลเฉพาะตอนที่บอร์ดยังเปิดเครื่องอยู่
เท่านั้น หลังจากปิดเครื่อง/reboot แล้ว swap จะกลับมาทํางานเหมือนเดิม ต้องรัน command นี้อีก
ครั้งหนึ่งเพื่อปิด swap นี้
2.4. (อย่าข้ามขั้นตอนนี้!) รันคําสั่ง sudo sysctl net.bridge.bridge-nf-call-iptables=1
2.5. เพิ่ม Kubernetes Repo List โดยแก้ไขไฟล์ /etc/apt/sources.list.d/kubernetes.list โดยใช้คําสั่ง
sudo nano /etc/apt/sources.list.d/kubernetes.list แล้วเพิ่มบรรทัดนี้ลงไฟล์
deb http://apt.kubernetes.io/ kubernetes-xenial main

2.6. เพิ่ม GPG key ของ Kubernetes โดยใช้คําสั่ง
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add –
หาก add key ได้สําเร็จ จะแสดงผลต่อว่า OK
2.7. รันคําสั่ง sudo apt-get update
2.8. ติดตั้ง kubeadm, kubectl โดยใช้คําสั่ง sudo apt-get install -qy kubeadm
2.9. หลังติดตั้งเสร็จแล้ว ทดลองรันคําสั่ง kubeadm และ kubectl ด้วยว่ารันได้หรือไม่
3. Master Node Setup
หมายเหตุ ณ 29 พ.ย. 62:
สําหรับ Master Node ได้ทดลองใช้เป็น VM ของ Ubuntu 16.04 ที่ได้ตั้งค่า common setup ตาม
ขั้นตอนข้างต้นไว้แล้ว ตรวจสอบด้วยว่า Master Node เหลือพื้นที่ว่างขั้นต่ํา 5-10 GB เพื่อใช้สําหรับลง
Kubernetes Cluster และ VM รันโดยใช้ 2 Processor (แนะนําให้ใช้RAM 2 GB)
หาก Virtual Disk เหลือที่ว่างน้อยกว่ากําหนด สามารถขยายพื้นที่ได้ สําหรับ Ubuntu VM อ่านเพิ่มเติม
ที่ http://derekmolloy.ie/resize-a-virtualbox-disk
กรณีใช้ VM ต้องตั้ง Network Adapter เพิ่มเป็น 2 อัน คือ
- NAT (สําหรับต่อ VM เข้าสู่อินเทอร์เน็ต)
- Bridged Adapter (สําหรับเชื่อมสัญญาณจาก LAN ที่ต่อเข้ากับ pi เข้าสู่ VM ด้วย โดย IP
ใน VM จะถัดจาก IP ของ notebook เช่น 192.168.99.100 -> 192.168.99.101)
3.1. Pre-pull Image
เข้าไปที่ terminal รันคําสั่ง sudo kubeadm config images pull -v3 รอจนกว่าจะดึง Kubernetes
images จนเสร็จ
3.2. Kubeadm Initialization
ถ้าจะใช้ Weave Net เป็น Network Overlay ให้รัน command
sudo kubeadm init --token-ttl=0
โดย 192.168.99.X เป็น IP ของ Ethernet ใน VM (ดูได้จากคําสั่ง ifconfig)
ถ้าจะใช้ Flannel เป็น Network Overlay ให้รัน command
sudo kubeadm init --token-ttl=0 --pod-network-cidr=10.244.0.0/16
โดย 192.168.99.X เป็น IP ของ Ethernet ใน VM (ดูได้จากคําสั่ง ifconfig)
3.3. เมื่อ initialize เสร็จแล้ว จะพบคําสั่ง join และ join token ในรูปแบบนี้:

kubeadm join --token 9e700f.7dc97f5e3a45c9e5 192.168.0.27:6443 --discovery-token-ca-cert-
hash sha256:95cbb9ee5536aa61ec0239d6edd8598af68758308d0a0425848ae1af28859bea

ให้ copy คําสั่งที่พบ (ตั้งแต่คําว่า kubeadm join จนถึง hash) ไว้ใน clipboard เพราะเราจะใช้คําสั่งนี้บน
worker node ในการ join เข้า cluster
3.4. รัน snippet นี้ต่อบน terminal
mkdir -p $HOME/.kube && \
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config && \
sudo chown $(id -u):$(id -g) $HOME/.kube/config
3.5. กรณีใช้ Weave Net ให้ติดตั้ง Weave Net Driver บน Master Node โดยใช้คําสั่ง

kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
กรณีใช้ Flannel ให้ติดตั้ง Flannel Driver บน Master Node โดยใช้คําสั่ง
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

3.6. ตรวจสอบว่า cluster สร้างขึ้นมาเรียบร้อย โดยดูผลจากสองคําสั่งนี้:
- kubectl get pods --namespace=kube-system
ทุก pods ต้องมี status เป็น Running
- kubectl get nodes
จะมีnode ที่เป็น master เพียงตัวเดียว และอยู่ในสถานะ Ready
4. Worker Node Setup
4.1. ใน worker node ให้นําคําสั่ง join และ join token จากข้อ 3.3 มารันใน terminal ของ worker
node ก่อนรัน เช็คด้วยว่า swap เปิดอยู่หรือเปล่า โดยใช้คําสั่ง sudo swapon –summary ถ้าไม่มี
ผลลัพธ์ใด ๆ แสดงขึ้นมา แสดงว่าปิด swap แล้ว แต่ถ้ามีผลลัพธ์ swap โผล่ขึ้นมา ให้ปิด swap ก่อน (ดู
ข้อ 2.3)
4.2. ถ้า join สําเร็จ จะขึ้นผลว่า This node has joined the cluster
4.3. กลับไปที่หน้า terminal ของ Master Node แล้วใช้คําสั่ง

- kubectl get nodes ดูว่า worker nodes join เข้ามาหรือไม่
- kubectl get pods --namespace=kube-system ดูว่า pods ของ weave/flannel
สร้างมารองรับ node ใหม่ให้หรือไม่

ตัวอย่างรูปด้านล่าง แสดงให้เห็นว่า nodes ใหม่ได้ joined เข้ามาแล้ว แต่ว่าสร้าง pod ของ weave มา
รองรับ node ใหม่ไม่สําเร็จ (สังเกตที่ status ErrImagePull, ImagePullBackOff) ทําให้ nodes ใหม่
อยู่ในสถานะ NotReady (เป้าหมายของเราคือ nodes ที่เข้ามาใหม่ต้องอยู่ในสถานะ Ready)
4.4. หากต้องการหยุดการทํางาน/reset การทํางานของ kubeadm ให้ใช้คําสั่ง sudo kubeadm reset
เพื่อ reset การทํางานทั้งหมด โดยรันคําสั่งนี้ใน worker nodes ให้ครบก่อน แล้วค่อยรันคําสั่งนี้ใน
master nodes **ก่อนปิดเครื่อง/ต้องการ reset ตัว master node ใหม่ รันคําสั่งนี้ทุกครั้ง***
5. วิธีแก้ปัญหา worker nodes อยู่ในสถานะ NotReady ที่ไม่ได้ผล
วิธีแก้ปัญหาด้านล่างนี้ เป็นวิธีการที่ลองใช้เพือแก้ปัญหา worker nodes joined the cluster ได้ แต่ไม่
สามารถทํางานได้ อยู่ในสถานะ NotReady แต่ว่าไม่ได้ผล (เพราะฉะนั้นจึงไม่จําเป็นต้องลองใช้วิธีเหล่านี้ซ้ํา)
5.1. Switch iptables toolings to legacy (วิธีที่ภณแนะนําในกลุ่ม SDS)

Reference: https://kubernetes.io/docs/setup/production-
environment/tools/kubeadm/install-kubeadm/#ensure-iptables-tooling-does-not-use-
the-nftables-backend

5.2. ติดตั้ง Flannel Driver โดยระบุให้ลงเฉพาะ driver ของ ARM ซึ่งจริง ๆ แล้ว หากใช้คําสั่งติดตั้ง
flannel driver ข้างต้น ระบบก็จะติดตั้ง driver สําหรับทุก architecture ไว้อยู่แล้ว ไม่จําเป็นต้องระบุ
ไว้ตอนติดตั้ง driver
Reference: https://github.com/coreos/flannel/issues/663 และขั้นตอนที่ 9 ของ Kubernetes

Master Nodes ใน https://medium.com/@davyhua/raspberry-pi-kubernetes-cluster-setup-
as-of-v1-11-2-95e293c1a2f3

5.3. ติดตั้ง Flannel RBAC ก่อนติดตั้ง Flannel Driver ซึ่งได้ทดลองวิธีนี้แล้ว ปัญหาก็ยังคงอยู่เหมือนเดิม

Reference: https://stackoverflow.com/questions/43662229/flannel-installation-on-
kubernetes และ https://medium.com/sirisoft/getting-start-with-kubernetes-
5b4e6b89241e
