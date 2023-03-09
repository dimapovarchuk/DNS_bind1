# DNS_bind

# Configuring BIND DNS Server
You’ve now installed BIND packages on the Ubuntu server, so it’s time to set up the BIND installation on your Ubuntu server.

All configuration for BIND is available at the /etc/bind/ directory, and configurations for the named service at /etc/default/named.

Edit the /etc/default/named configuration using your preferred editor and add option -4 on the OPTIONS line, as shown below. This option will run the named service on IPv4 only.

<img width="537" alt="image" src="https://user-images.githubusercontent.com/52627259/224014202-e1d82164-2d0b-4f16-b323-90714dc3c912.png">

Save the changes you made and close the file.

Next, edit the /etc/bind/named.conf.options file and populate the following configuration below the directory "/var/cache/bind"; line.
This configuration sets the BIND service to run on default UDP port 53 on the server’s localhost and public IP address (10.0.2.15). At the same time, it allows queries from any host to the BIND DNS server using the Cloudflare DNS 1.1.1.1 as the forwarder.

<img width="429" alt="image" src="https://user-images.githubusercontent.com/52627259/224077064-5bcd491b-8c12-4f14-b7c4-758e37acc14e.png">

At the bottom, comment out the listen-on-v6 { any; }; line, to disable the named service from running on IPv6.

Lastly, run the following command to verify the BIND configuration.
sudo named-checkconf
If there’s no output, the BIND configurations are correct without any error.

<img width="296" alt="image" src="https://user-images.githubusercontent.com/52627259/224017733-86a694b5-166c-411a-9aca-8eecc16122aa.png">

Setting Up DNS Zones
At this point, you’ve configured the basic configuration of the BIND DNS Server. You’re ready to create a DNS Server with your domain and add other sub-domains for your applications. You’ll need to define and create a new DNS zones configuration to do so.

In this tutorial, you’ll create a new Name Server (ns1.dpovarchuk.com) and sub-domains (www.dpovarchuk.com, mail.dpovarchuk.com, vault.dpovarchuk.com).

Edit the /etc/bind/named.conf.local file using your preferred editor and add the following configuration.
This configuration defines the forward zone (/etc/bind/zones/forward.dpovarchuk.com), and the reverse zone (/etc/bind/zones/reverse.dpovarchuk.com) for the dpovarchuk.com domain name.

<img width="520" alt="image" src="https://user-images.githubusercontent.com/52627259/224025762-66fb120d-bd49-428e-b91a-9962ea30f1be.png">

Save the changes and close the file.

Next, run the below command to create a new directory (/etc/bind/zones) for string DNS zones configurations.

mkdir -p /etc/bind/zones/

Run each command below to copy the default forward and reverse zones configuration to the /etc/bind/zones directory.

<img width="587" alt="image" src="https://user-images.githubusercontent.com/52627259/224029939-7419b582-81a3-4a65-b0dd-fc0ddfef97bb.png">

Now, edit the forward zone configuration (/etc/bind/zones/forward.dpovarchuk.com) using your preferred editor and populate the configuration below.
The forward zone configuration is where you define your domain name and the server IP address. This configuration will translate the domain name to the correct IP address of the server.

The configuration below creates the following name server and sub-domains:

ns1.dpovarchuk.com – The main Name Server for your domain with the IP address 10.0.2.15.
MX record for the dpovarchuk.com domain that is handled by the mail.dpovarchuk.com. The MX record is used for the mail server.
Sub-domains for applications: www.dpovarchuk.com, mail.dpovarchuk.com, and vault.dpovarchuk.com.

<img width="454" alt="image" src="https://user-images.githubusercontent.com/52627259/224057439-b93a18ca-7173-4b24-b5b7-dad5e86af7e8.png">

Save the changes and close the file.

Like the forward zone, edit the reverse zone configuration file (/etc/bind/zones/reverse.dpovarchuk.com) and populate the following configuration.
The reverse zone translates the server IP address to the domain name. The reverse zone or PTR record is essential for services like the Mail server, which affects the Mail server’s reputation.

The PTR record uses the last block of the IP address, like the PTR record with the number 10 for the server IP address 172.16.1.10.

This configuration creates the reverse zone or PTR record for the following domains:

Name server ns1.dpovarchuk.com with the reverse zone or PTR record 10.0.2.15.
PTR record for the domain mail.dpovarchuk.com to the server IP address 10.0.2.15.

<img width="463" alt="image" src="https://user-images.githubusercontent.com/52627259/224059300-c72988ff-eb98-4edc-b314-84eeeaa443fa.png">

Save the changes and close the file.

Now, run the following commands to check and verify BIND configurations.
When your configuration is correct, you’ll see an output similar below.

<img width="626" alt="image" src="https://user-images.githubusercontent.com/52627259/224062774-6c1038c4-0b2c-4538-a9bb-f5e659d740d6.png">

Lastly, run the systemctl command below to restart and verify the named service. Doing so applies new changes to the named service.

Below, you can see the named service status is active (running).

<img width="662" alt="image" src="https://user-images.githubusercontent.com/52627259/224080262-f5483574-e836-461d-a45c-b63c46fad072.png">

Verifying BIND DNS Server Installation
You’ve now completed the BIND DNS installation. But how do you verify your DNS Server installation? The dig command will do the trick.

Dig is a command-line utility for troubleshooting DNS Server installation. dig performs DNS lookup for the given domain name and displays detailed answers for the domain name target. On the Ubuntu system, dig is part of the bind9-dnsutil package.

To verify your BIND DNS server installation:

Run each dig command below to verify the sub-domains www.dpovarchuk.com, mail.dpovarchuk.com, and vault.dpovarchuk.com.
If your DNS Server installation is successful, each sub-domain will be resolved to the correct IP address based on the forward.dpovarchuk.com configuration.

Checking the domain names
dig @10.0.2.15 www.dpovarchuk.com
dig @10.0.2.15 mail.dpovarchuk.com
dig @10.0.2.15 vault.dpovarchuk.com

Below is the output of the sub-domain www.dpovarchuk.com resolved to the server IP address 10.0.2.15.

<img width="517" alt="image" src="https://user-images.githubusercontent.com/52627259/224085266-09098ba4-ebc1-40ee-a06c-99e4e5eadc1c.png">

Below is the output of the sub-domain mail.dpovarchuk.com resolved to the server IP address 10.0.2.25.

<img width="546" alt="image" src="https://user-images.githubusercontent.com/52627259/224101845-a76a2909-0231-4c11-9258-7a1e385f660b.png">

Below is the output of the sub-domain vault.dpovarchuk.com resolved to the server IP address 10.0.2.35.

<img width="523" alt="image" src="https://user-images.githubusercontent.com/52627259/224102004-f0fdf5c7-2292-4626-932a-a1db12c78288.png">

Next, run the dig command below to verify the MX record for the dpovarchuk.com domain.

dig @10.0.2.15 dpovarchuk.com MX

You should see the dpovarchuk.com domain has the MX record mail.dpovarchuk.com.

<img width="511" alt="image" src="https://user-images.githubusercontent.com/52627259/224103055-9612fffd-538f-44e3-9bc4-d68b857d3903.png">

Lastly, run the following commands to verify the PTR record or reverse zone for the server IP addresses 10.0.2.15 and 10.0.2.25.
If your BIND installation is successful, each IP address will be resolved to the domain name defined on the reverse.dpovarchuk.com configuration.

Checking PTR record or reverse DNS
dig @10.0.2.15 -x 10.0.2.15
dig @10.0.2.15 -x 10.0.2.25

You can see below, the server IP address 10.0.2.15 is resolved to the domain name ns1.dpovarchuk.com.

<img width="599" alt="image" src="https://user-images.githubusercontent.com/52627259/224104842-cd263ed9-0ff1-4a2b-83ac-01ff051b40fd.png">

As you see below, the server IP address 10.0.2.25 is resolved to the domain name mail.dpovarchuk.com.

<img width="598" alt="image" src="https://user-images.githubusercontent.com/52627259/224105527-ddd7e067-6f16-47f1-baf5-b65fb4a92b04.png">







