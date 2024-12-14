1. Create VPC 11.0.0.0/16
2. Create Subnets which is lesser than VPC range 11.0.1.0/24 (public) 11.0.2.0/24 (private)
3. Create Internet Gateway (IGW) 
4. Create Route Tables (Public and Private both)
5. Asociate the subnets WRT to route tables.
6. Create routes for IGW, So our whole network knows how to access internet via this IGW.
7. Create NAT for access internet from private subnet  (Wait for getting this in Available state)
8. Associate Private Subnet to this NGW (Go to RT -- select Private subnet -- edit routes -- add routes)
