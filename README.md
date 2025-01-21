# Membuat autoscaling berkoneksi dengan efs dan mengarah ke elb

**TAG**: *AWS Load Balancer & Auto Scalling koneksi ke efs dengan 2 avability zone (oregon)*

---

![ftoo Gambar](https://raw.githubusercontent.com/ibrammanggara/load-test-aws/main/ftoo.png)



## Langkah membuat VPC

1. masukkan nama vpc : VPC-SAKTI
2. masukkan ip cidr IPv4 : 192.168.0.0/16
3. save
4. edit vpc setting : ceklis enable dns hostname

---

## Langkah membuat 4 subnet (2 publik subnet & 2 privat subnet)

1. pilih vpc : VPC-SAKTI
2. masukan nama subnet : SUBNET PUBLIK 1A
3. pilih az : us-east-1a
4. masukkan IPv4 subnet CIDR block : 192.168.10.0/24
   ### (+) add new subnet
1. masukan nama subnet : SUBNET PRIVAT 1A
2. pilih az : us-east-1a
3. masukkan IPv4 subnet CIDR block : 192.168.20.0/24
   ### (+) add new subnet
1. masukan nama subnet : SUBNET PUBLIK 1B
2. pilih az : us-east-1b
3. masukkan IPv4 subnet CIDR block : 192.168.30.0/24
   ### (+) add new subnet
1. masukan nama subnet : SUBNET PRIVAT 1B
2. pilih az : us-east-1b
3. masukkan IPv4 subnet CIDR block : 192.168.40.0/24
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
5. SUBNET PUBLIK 1B
6. SUBNET PUBLIK 1A

---
