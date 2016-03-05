# docker-mailrelay
## Mail gateway using Amazon SES

This is the docker image build for my internal mail relay service, using postfix.  It has been customized to use Amazon SES, per https://docs.aws.amazon.com/ses/latest/DeveloperGuide/postfix.html

## Inputs

1. Either:
  a. name your container's hostname, as postfix will set its myhostname var with that.   Docker switch "--hostname your.hostname.com"
  b. set env variable "myhostname" to the hostname you want your mail relay to know itself by

2. Set env variable "mynetworks" to the appropriate string representing subnets to allow mail from.  If not set, the default, which will work for localhost and other docker containers, is:
		mynetworks = 127.0.0.0/8, 172.17.0.0/16

3. Map your sasl_passwd file to /etc/postfix/sasl_passwd.   The contents should contain your amazon credentials and look something like:
```
    [email-smtp.us-east-1.amazonaws.com]:587 AKIAIZY3D2SA6JSCSEBQ:AusMuXwQcquQN19UQ8MJXdAifFi/LRLivx2j3IrU8r9I
```

## Sample Run:

```
docker run -d \
        --name mailrelay \
        --restart on-failure \
        -p 10.1.1.8:25:25 -p 127.0.0.1:25:25 \
        --hostname mailrelay \
        -e myhostname=mailrelay.guyton.net \
        -e mynetworks="10.1.1.0/24, 127.0.0.0/8, 172.17.0.0/16" \
        -v /root/dockerfiles/sasl_passwd:/etc/postfix/sasl_passwd \
        guyton/mailrelay
```
