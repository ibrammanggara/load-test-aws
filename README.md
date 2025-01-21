# Membuat autoscaling berkoneksi dengan efs dan mengarah ke elb

**TAG**: *AWS Load Balancer & Auto Scalling koneksi ke efs dengan 2 avability zone (oregon)*

---

![ftoo Gambar](https://raw.githubusercontent.com/ibrammanggara/load-test-aws/main/ftoo.png)



## Langkah membuat VPC

1. masukkan nama vpc : VPC-SAKTI
2. masukkan ip cidr IPv4 : 10.100.0.0/16
3. save
4. edit vpc setting : ceklis enable dns hostname

---

## Langkah membuat 4 subnet (2 publik subnet & 2 privat subnet)

1. pilih vpc : VPC-SAKTI
2. masukan nama subnet : public subnet 2a
3. pilih az : us-west-2a
4. masukkan IPv4 subnet CIDR block : 10.100.1.0/28
   ### (+) add new subnet
1. masukan nama subnet : public subnet 2b
2. pilih az : us-west-2b
3. masukkan IPv4 subnet CIDR block : 10.100.2.0/28
   ### (+) add new subnet
1. masukan nama subnet : private subnet 2a
2. pilih az : us-west-2a
3. masukkan IPv4 subnet CIDR block : 10.100.10.0/24
   ### (+) add new subnet
1. masukan nama subnet : private subnet 2b
2. pilih az : us-west-2b
3. masukkan IPv4 subnet CIDR block : 10.100.20.0/24
4. save

   ## edit semua subnet publik
1. Enable auto-assign public IPv4 address
   
---

## membuat internet gateway

1. masukan nama igw : IGW-SAKTI
2. attach ke vpc : VPC-SAKTI

---

## langkah membuat 2 routable (publik dan privat)

 ### publik
1. masukan nama routable : RT-PUBLIK SAKTI
2. pilih vpc : VPC-SAKTI
3. save
 ### privat
1. masukan nama routable : RT-PRIVAT SAKTI
2. pilih vpc : VPC-SAKTI
3. save

---

## langkah mengkaitkan routable (publik)

### RT-PUBLIK SAKTI
1. #add route
2. destination : 0.0.0.0/0
3. target : IGW-SAKTI
4. #subnet associations
5. public subnet 2a
6. public subnet 2b

---

### NAT GATEWAY

1. masuk nat gateways
2. nama : NGW-SAKTI
3. subnet : public subnet 2b
4. (allocate elastic ip)
5. save

---

## langkah mengkaitkan routable (privat)

### RT-PRIVAT SAKTI
1. #add route
2. destination : 0.0.0.0/0
3. target : nat gateway > NGW-SAKTI
4. #subnet associations
5. private subnet 2a
6. private subnet 2b

---

## langkah membuat 2 SECURITY GROUP

1. masukan nama : sakti-keamanan
2. desk : untuk ec2 keamanan
3. vpc : VPC-SAKTI
4. inBound rules : ssh = 22 | http = 80 | https = 443 | all-icmp Ipv4 | (all anywhere - IPv4)
5. save

1. masukan nama : sakti-keamanan-efs
2. desk : untuk efs keamanan
3. vpc : VPC-SAKTI
4. inBound rules : nfs = 2049 > source = id sg(sakti-keamanan)
5. save

1. edit : sakti-keamanan
2. outBound rules : nfs = 2049 > source = id sg(sakti-keamanan-efs)
3. save

---

## langkah membuat EC2 (EC2 bastion)

1. masukan nama ec2 : EC2 bastion
2. ami : ubuntu 22.04/24.04
3. instance type : t2.micro
4. key pair : sakti.pem
5. network setting : VPC-sakti, public subnet 2b, ip publik Enable
6. pilih security gruop : nama = sakti-keamanan
7. storage : 8GB
8. save

---

## langkah membuat EC2 (load test app)

1. masukan nama ec2 : load test app
2. ami : amazon linux 2
3. instance type : t2.micro
4. key pair : sakti.pem
5. network setting : VPC-sakti, private subnet 2a, ip publik Disable
6. pilih security gruop : nama = sakti-keamanan
7. storage : 8GB
8. save

---

## langkah membuat EFS

1. nama : efs
2. klik (costumize)
3. unceklis auto backup
4. Lifecycle management : all none
5. next
6. vpc : VPC-sakti
7. mount target : us-west-2b > subnet privat 2b > sg : sakti-keamanan-efs
8. next
9. next
10. save

---

## masuk ke ec2 (load test app) dengan bastion untuk installasi dan mount efs

1. menu efs klik atach : mount via ip
2. copy kode mount efs
3. di ec2 (load test app)
4. $sudo mkdir -p /var/www/html
5. $(paste kode nya dan ganti alamat mount [efs] ke [/var/www/html])
6. $sudo nano /etc/fstab
7. #pasang = ip:/ /var/www/html nfs4 nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport 0 0
8. $sudo mount -a
9. $sudo mount -s
10. #cek keterangan (ok) jika muncul maka telah berhasil anda bisa mencoba reboot sistem
11. $sudo reboot
12. #step 2
13. #pasang paket yang di perlukan dan import file load test cpu ke /var/www/html
14. #exit ke ec2 bastion
15. curl ip private(load test app)
16. jika terdapat kode html dari source code yang sesuai maka sudah berhasil

---

## membuat AMIs dengan ec2(load test app)

1. di panel ec2, klik instance ec2 (load test app)
2. klik actions > image and templates > create image
3. nama image : image-ec2-efs
4. desk : ec2 dengan hubungan efs
5. save
6. #memerlukan waktu untuk image siap digunakan

---

## membuat launch template dengan AMIs

1. 

---


















