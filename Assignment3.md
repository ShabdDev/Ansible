### Tasks To Be Performed:
1. Create 2 Ansible roles
2. Install Apache2 on slave1 using one role and
   NGINX on slave2 using the other role
4. Above should be implemented using different Ansible roles

## Solution
1. Create 3 EC2 instances(ubuntu)
2. connect them
   
### On Master(EC2 - 1)
1. Update the machine
   ```
   sudo apt update
   ```
2. Create the script file
   ```
   sudo nano installationscript.sh
   ```
3. go to official website
    (https://docs.ansible.com/projects/ansible/latest/installation_guide/installation_distros.html#installing-ansible-on-ubuntu)
    copy commands and paste inside script
    ```
    sudo apt update
    sudo apt install software-properties-common
    sudo add-apt-repository --yes --update ppa:ansible/ansible
    sudo apt install ansible
    ```
4. Run the script
   ```
   bash installationscript.sh
   ```
5. Go to ssh folder
   ```
   cd .ssh
   ```
6. Generate Public and private rsa key pair
   ```
   ssh-keygen
   ls
   ```
7. Copy the public key and paste it in slave1 and slave2 machine's .ssh-> authorized_keys file
   ```
   sudo cat id_rsa_pub
   ```
8. Go to inventory files and copy the private Ip's of slave machines
   ```
   cd /etc/ansible/
   ```
9. open inventory file
    ```
    sudo nano hosts
    ```
10. paste private Ip addresses here along with label(as tasks are different on both machines)
    ```
    [slave-1]
    <slave1 ip>
    [slave-2]
    <slave2 ip>
    ```
11. Ping the machines
    ```
    ansible -m ping all
    ```
12. look for roles folder and create role there
    ```
    ls
    cd roles
    sudo ansible-galaxy init apache-role
    ls
    ```
13. Go inside created role folder and look for task folder
    ```
    cd apache-role
    ls
    cd tasks
    ls
    ```
14. create playbook here inside task folder and mention playbook's name inside main.yml
    ```
    sudo nano installation_apache.yaml
    ---
     - name: installing apache2
       apt: name=apache2 state=latest
    ```
15. call this yaml inside main.yml
    ```
    nano main.yml
    
    ---
     - include_tasks: installation_apache.yaml
    
    cd ../../..
    ```
16. pwd should be /etc/roles
17. create playbook play3.yaml
    ```
    sudo nano play3.yaml

    ---
     - name: executing roles inside slave 1
       hosts: slave-1
       become: true
       roles:
        - apache-role
    ```
18. Check the syntax of playbook
    ```
    ansible-playbook play3.yaml --syntax-check
    ```
19. run the playbook
    ```
    ansible-playbook play3.yaml
    ```
20. Create one more role inside roles folder
    ```
    sudo ansible-galaxy init nginx-role
    ls
    cd tasks
    ls
    sudo nano installation-nginx.yaml
     ---
     - name: installing nginx
       apt: name=nginx state=present
    
     nano main.yml
    
    ---
     - include_tasks: installation-nginx.yaml
    
    cd ../../..

     sudo nano play3.yaml

    ---
     - name: executing roles inside slave 1
       hosts: slave-1
       become: true
       roles:
        - apache-role
    
     - name: executing roles inside slave 2
       hosts: slave-2
       become: true
       roles:
        - nginx-role

    ansible-playbook play3.yaml --syntax-check
    ansible-playbook play3.yaml
    
    ```
    
### On Slave1(EC2 - 2)
1. Update the machine
   ```
   sudo apt update
   ```
2. Go to ssh folder
   ```
   cd .ssh
   ```
3. open file authorized_keys
   ```
   nano authorized_keys
   ```
4. paste copied id_rsa_pub key and save the file
5. after step 19 of master's machine completed check if apache is installed here
6. copy the IP of slave machine and paste in the browser  
   
### On Slave2 (EC2 - 3)
1. Update the machine
   ```
   sudo apt update
   ```
2. Go to ssh folder
   ```
   cd .ssh
   ```
3. open file authorized_keys
   ```
   nano authorized_keys
   ```
4. paste copied id_rsa_pub key and save the file
5. after 20th step check if nginx is installed on slave2 same as we checked for slave1
