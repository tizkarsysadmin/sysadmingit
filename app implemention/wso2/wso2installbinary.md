Step by step install wso2am in ubuntu server 24.04
As a service

apt update

apt upgrade -y

apt install openjdk-17-jdk -y

java -version


update-alternatives --config java


vi /etc/environment

add:
JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64

source /etc/environment


echo $JAVA_HOME

/usr/lib/jvm/java-17-openjdk-amd64


vi /etc/bash.bashrc

export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64


export PATH=${JAVA_HOME}/bin:${PATH}


apt install unzip
mkdir /var/wso2am

cd /var/wso2am
cp /home/user01/wso2/wso2am-4.3.0.zip ./
unzip wso2am-4.3.0.zip



sh /var/wso2am/wso2am-4.3.0/bin/api-manager.sh start

step1:
The Registered callback does not match with the provided URL error can be encountered during the API Publisher(https://<hostname>:9443/publisher) and API Developer Portal (https://<hostname>:9443/devportal) login attempts, in a case where the hostname of the API Manager has been changed after accessing the Developer Portal or Publisher apps via different hostnames.
For example, let's assume that you have started a fresh APIM server and accessed the API Publisher and Developer Portal apps via localhost. If you have changed the hostname of the server from localhost to apim.wso2.com, the next login attempt to API Publisher or Developer Portal will be failed giving this error.

This error has been occurred due to the mismatch of the API Publisher or API Developer Portal access URLs((https://<hostname>:9443/publisher and https://<hostname>:9443/devportal) and callback URLs which are configured in API Publisher and API Developer Portal Service Providers.
Please follow below steps to fix the login failure due to callback URL mismatch.
Sign in to the Management Console (https://<hostname>:9443/carbon).
Navigate to service providers list.
 
Click on the Edit button of API Publisher service provider
 
Navigate to Inbound Authentication Configuration > OAuth/OpenID Connect Configuration and click on OAuth application edit button.
 
See the Callback Url regex value configured under Application Settings. You will observe that the callback URL value is having a different hostname(localhost or previous hostname which was configured before the hostname change).
regexp=(https://localhost:9443/publisher/services/auth/callback/login|https://localhost:9443/publisher/services/auth/callback/logout)
Then replace the callback URL hostname with the current hostname of the server. For example, if the current hostname of the server is apim.wso2.com, the callback URL regex has to be changes as follows.
regexp=(https://apim.wso2.com:9443/publisher/services/auth/callback/login|https://apim.wso2.com:9443/publisher/services/auth/callback/logout)
Click on Update button in Applications Settings page, then the Update button of service provider information page to save the callback URL change.
Select the service provider for API Developer Portal (admin_admin_store) and repeat the step 4 - step 6 to apply the same changes.

Below string:
regexp=(https://localhost:9443/publisher/services/auth/callback/login|https://localhost:9443/publisher/services/auth/callback/logout)
replace with:
regexp=(https://192.168.221.135:9443/publisher/services/auth/callback/login|https://192.168.221.135:9443/publisher/services/auth/callback/logout)


regexp=(https://localhost:9443/devportal/services/auth/callback/login|https://localhost:9443/devportal/services/auth/callback/logout)
replace with:
regexp=(https://192.168.221.135:9443/devportal/services/auth/callback/login|https://192.168.221.135:9443/devportal/services/auth/callback/logout)

step2:

Open the /var/wso2am/wso2am-4.3.0/repository/conf/deployment.toml file
Define the hostname attribute under server configurations as shown below.
[server]
hostname = "{hostname}"
Replace {hostname} with the Hostname or IP address of the machine hosting this server. This becomes part of the endpoint reference of the services deployed on this server instance.
Configure the Developer Portal URL, which is used to access the Developer Portal via the Publisher.
Uncomment the following configuration and define the hostname.
[apim.devportal]
url = "https://<hostname>:${mgt.transport.https.port}/devportal"

step3:

After we have gotten the API working, we need a few steps in order to consume a new API.
Open the /var/wso2am/wso2am-4.3.0/repository/conf/carbon.xml file and set the HostName and MgtHostName property as shown below.
<HostName>{hostname}</HostName>
<MgtHostName>{management-hostname}</MgtHostName>
{hostname} — Hostname or IP address of the machine hosting this server. This is will become part of the End Point Reference of the services deployed on this server instance.
{management-hostname} — Hostname to be used for the WSO2 API-M Management console.

Step4:

Edit /var/wso2am/wso2am-4.3.0/repository/conf/api-manager.xml file and change the properties GatewayEndpoint and GatewayWSEndpoint
<GatewayEndpoint>http://<yourhost>:${http.nio.port},https://<yourhost>:${https.nio.port}</GatewayEndpoint>
<! — Endpoint of the Websocket APIs hosted in this API Gateway →
<GatewayWSEndpoint>ws://<yourhost>:9099</GatewayWSEndpoint>




cd /var/wso2am/wso2am-4.3.0/bin
sh api-manager.sh start

 


https://192.168.221.135:9443/publisher
https://192.168.221.135:9443/devportal/apis

https://192.168.221.135:9443/carbon/


admin
admin

 


 

 
 

Now stop the process to make it automate and run it by making it as linux service:
sudo groupadd wso2
useradd -g wso2 -m -d /var/wso2am/wso2am-4.3.0 -s /bin/bash wso2
chown -R wso2:wso2 /var/wso2am/wso2am-4.3.0



mkdir /etc/systemd/system/wso2am.service.d

vi /etc/systemd/system/wso2am.service.d/myenv.conf

[Service]
Environment=JAVA_HOME="/usr/lib/jvm/java-17-openjdk-amd64"
Environment=CARBON_HOME="/var/wso2am/wso2am-4.3.0"


vi /etc/systemd/system/wso2am.service


[Unit]
Description=wso2am server daemon
Documentation=https://docs.wso2.com/
After=network.target

[Service]
EnvironmentFile='/etc/systemd/system/myenv.conf'
ExecStart=/bin/sh -c '/var/wso2am/wso2am-4.3.0/bin/api-manager.sh start'
ExecStop=/bin/sh -c '/var/wso2am/wso2am-4.3.0/bin/api-manager.sh stop'
ExecRestart=/bin/sh -c '/var/wso2am/wso2am-4.3.0/bin/api-manager.sh restart'
PIDFile=/var/wso2am/wso2am-4.3.0/wso2carbon.pid
User=wso2
Group=wso2
Type=forking
Restart=always
RestartSec=2
StartLimitInterval=60s
StartLimitBurst=3
StandardOutput=journal

[Install]
WantedBy=multi-user.target


systemctl daemon-reload
systemctl enable wso2am.service
or enable and start
systemctl enable --now wso2am.service
systemctl start wso2am.service

if in this section encountered a problem debug system with 
journalctl -xeu wso2am.service


change admin password:

admin
admin

to change default admin password go to carbon ui:
 

 
Change password to desired one and sign in again

Also go to :

vi /var/wso2am/wso2am-4.3.0/repository/conf/deployment.toml

change :
[super_admin]
    username = "your-name"
    password = "your-password"

if u use special character in password content then ,make below change:
   [super_admin]
   username = "your-name"
   password = "<![CDATA[your-password]]>" 

   [apim.throttling.jms]
   username = "your-name"
   password = "your-encoded-password"        

