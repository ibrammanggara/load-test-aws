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

g

---

g

---
