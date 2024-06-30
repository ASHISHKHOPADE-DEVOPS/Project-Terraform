

step 0 : aws account credantials

provider "aws" {
  region     = "us-west-2"
  access_key = ""
  secret_key = ""
}


step 1 : create the vpc "10.0.0.0/16"


}
resource "aws_vpc" "ashish" {
  cidr_block       = "10.0.0.0/16"
  instance_tenancy = "default"

  tags = {
    Name = "ashishvpc"
  }
}

step 2 : create the public subnet

resource "aws_subnet" "public" {
  vpc_id     = aws_vpc.ashish.id  # Corrected VPC ID reference
  cidr_block = "10.0.1.0/24"

  tags = {
    Name = "public"
  }
}

step 3 : create the security group

resource "aws_security_group" "ASH" {
  name        = "ASH"
  description = "Allow TLS inbound traffic"
  vpc_id      = aws_vpc.ashish.id

  ingress {
    description = "TLS from VPC"
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "ashishsg"
  }
}


step 4 :create the internet gateway

resource "aws_internet_gateway" "igw" {
  vpc_id = aws_vpc.ashish.id

  tags = {
    Name = "ashish"
  }
}

step 4 :create the route table 

resource "aws_route_table" "public" {
  vpc_id = aws_vpc.ashish.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.igw.id
  }


  tags = {
    Name = "public"
  }
}



step 5 :associate the subnet in route table
resource "aws_route_table_association" "publicaso" {
  subnet_id      = aws_subnet.public.id
  route_table_id = aws_route_table.public.id
}


step 6 :create the instance

resource "aws_instance" "ashish_instance" {
  ami           = "ami-01572eda7c4411960"
  instance_type = "t2.micro"
  subnet_id     = aws_subnet.public.id
  vpc_security_group_ids = [aws_security_group.ASH.id]
  tags = {
    Name = "ashishkhopade"
  }
}

step 7 :alloacte the elastic ip

resource "aws_eip" "kho" {
  instance = aws_instance.ashish_instance.id
  domain   = "vpc"

  
step 8 : created instance 
![Screenshot 2024-06-30 192502](https://github.com/ASHISHKHOPADE-DEVOPS/Project-Terraform/assets/172528313/a49f490a-ecf3-4c2c-b86c-046ddeeab7c3)



step 9:done
![Screenshot 2024-06-30 192545](https://github.com/ASHISHKHOPADE-DEVOPS/Project-Terraform/assets/172528313/bb49edb1-da3d-4d99-bb5b-266922596e69)
