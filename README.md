<h3 align="center">CloudHealth Linux Agent Installation Helpers</h3>
<hr/>

The CloudHealth Linux Agent allows us to collect instance-level metrics. 

The configuration details and [default settings can be viewed here](https://apps.cloudhealthtech.com/agent_settings/edit): 

```
wget https://s3.amazonaws.com/remote-collector/agent/v14/install_cht_perfmon.sh -O install_cht_perfmon.sh;
sudo sh install_cht_perfmon.sh 14 #{unique registration code} aws;
```



We have created CloudHealth Linux agent installation helpers for the commonly used provisioning automation tools. We have helpers for:
  
  - [Cloud Init](https://github.com/siddtewari/cht_agent_helpers#cloud-init---cloudhealth-agent-installation-helper) 
  - [Chef Recipe](https://github.com/siddtewari/cht_agent_helpers#chef-recipe---cloudhealth-agent-installation-helper)
  - [Ansible Playbook - BETA](https://github.com/siddtewari/cht_agent_helpers#ansible-playbook---cloudhealth-agent-installation-helper---beta)

<hr/>
##### Cloud Init - CloudHealth Agent Installation Helper
<hr/>

User Data that you can use with AWS as a bootstrap script. To learn more about cloud-init, please refer to the [AWS documentation](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html)

Replace unique-registration-code with your unique code. You can get the code from the agent configuration page https://apps.cloudhealthtech.com/agent_settings/edit

```
#!/bin/bash
# echo Defaults:root \!requiretty >> /etc/sudoers

#Install CHT linux agent
echo Defaults:root \!requiretty >> /etc/sudoers
wget https://s3.amazonaws.com/remote-collector/agent/v14/install_cht_perfmon.sh -O install_cht_perfmon.sh
sudo sh install_cht_perfmon.sh 14 UNIQUE_REGISTRATION_CODE aws

# ADDITIONAL SAMPLE CLOUD-INIT COMMANDS THAT YOU MIGHT HAVE
# yum update -y
# yum install httpd -y
# service httpd start
# yum install php56 php-mysql git wget -y


```
<hr/>
##### Chef Recipe - CloudHealth Agent Installation Helper
<hr/>

You can install the CloudHealth Agent using the following Chef Recipe. Replace unique-registration-code with your unique code. You can get this code from the agent configuration page https://apps.cloudhealthtech.com/agent_settings/edit

```
registration_code = "unique-registration-code" 

execute "Install Agent" do
  command "wget https://s3.amazonaws.com/remote-collector/agent/install_cht_perfmon.sh -O install_cht_perfmon.sh; sh install_cht_perfmon.sh 14 #{registration_code} aws; exit 0"
  ignore_failure true
  not_if "test -d /opt/cht_perfmon"
  only_if { node.attribute?(:ec2) }
end
```

<hr/>
##### Ansible Playbook - CloudHealth Agent Installation Helper - BETA 
<hr/>
 

```
---
- hosts: webservers
  vars:
    cht_unique_registration_code: ??
  tasks:
    - name: Ensure the agent is installed
      command:
        wget https://s3.amazonaws.com/remote-collector/agent/v14/install_cht_perfmon.sh -O /tmp/install_cht_perfmon.sh &&
        /tmp/install_cht_perfmon.sh 14 {{ cht_unique_registration_code }} aws
      args:
        creates: /opt/cht_perfmon
      sudo: yes
  
  ```