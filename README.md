# known

My deploy setup for Known on AWS.

# How To

1. Deploy ec2 instance
2. Attach EFS volume at ~/data
3. Get parameters for reaching RDS database
4. Install docker, docker-compose on ec2 instance
5. Move this repo to this instance
6. scp the env file to this instance
7. Ensure letsencrypt certs are up to date
8. Run `docker-compose up -d`

You can now access the Known instance at 80 and 443 on the machine.

## Encryption

Copy fullchain.pem and privkey.pem from /etc/letsencrypt/archive/$DOMAIN/ to
./certs (remove the trailing numbers from the file names). These will be passed
into the NGINX container.

TODO: Periodically check validity of certs and re-run certbot if they expire.

## Docker + VPC wrinkles

To reach other instances on the VPC (e.g. the database) Docker must be told to
use the VPC's DNS server; by default it uses Google's public DNS at 8.8.8.8. Go
to the docker options file (for ec2 this is `/etc/sysconfig/docker`) and change
the `OPTIONS` line to include `--dns 172.31.0.2 --dns 8.8.8.8 --dns 8.8.4.4`.
Amazon VPC DNS servers are usually at the base of the IP range +2 e.g. if the
VPC has block 172.31.0.0/16 then it will be at 172.31.0.2.

[Amazon Docker
Troubleshooting](http://docs.aws.amazon.com/AmazonECS/latest/developerguide/troubleshooting.html#docker-debug-mode)
[Amazon VPC
DNS](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_DHCP_Options.html#AmazonDNS)
