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

## langkah membuat EC2 (EC2 bastion)

1. masukan nama ec2 : EC2 bastion
2. ami : ubuntu 22.04/24.04
3. instance type : t2.micro
4. key pair : sakti.pem
5. network setting : VPC-sakti, public subnet 2b, ip publik Enable
6. buat security gruop : nama = sakti-keamanan | ssh = 22 | http = 80 | https = 443 | all-icmp Ipv4 | (all anywhere - IPv4)
7. storage : 8GB
8. save

---


