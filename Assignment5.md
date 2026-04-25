### Tasks To Be Performed:
1. Create a new deployment of Ansible cluster of 5 nodes
2. Label 2 nodes as test and other 2 as prod
3. Install Java on test nodes
4. Install MySQL server on prod nodes
5. Use Ansible roles for the above and group the hosts under test and prod.

### Solution
1. Create 5 EC2 instances(ubuntu)
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
3. go to official website to install ansible
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
7. Copy the public key and paste it in slave1, slave2, slave3, slave4 machine's .ssh-> authorized_keys file
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
    [test]
    <slave1 ip>
    <slave3 ip>
    
    [prod]
    <slave2 ip>
    <slave4 ip>
    ```
11. Ping the machines
    ```
    ansible -m ping all
    ```
12. create roles for java and mysql
    ```
    cd roles
    sudo ansible-galaxy init java-role
    sudo ansible-galaxy init mysql-role
    ```
13. go inside java role => tasks => create playbook here
    ```
    cd java-role
    ls
    cd tasks
    sudo nano installjava.yaml

    ---
     - name: installing java
       apt: name=openjdk-17-jdk state=latest
    
    ```
14. include this playbook inside main.yml
    ```
    ---
    - include_tasks: installjava.yaml
    ```
15. go inside mysql role => tasks => create playbook here
    ```
    cd mysql-role
    ls
    cd tasks
    sudo nano installmysql.yaml

    ---
     - name: installing mysql
       apt: name=mysql-server state=present
    
    ```
14. include this playbook inside main.yml
    ```
    ---
    - include_tasks: installmysql.yaml
    ```
15. create one playbook at /etc/ansible
    ```
    sudo nano play5.yaml
    ---
     - name: executing java role inside my test node
       hosts: test
       become: true
       roles:
        - java-role
    - name: executing mysql role inside my prod node
       hosts: prod
       become: true
       roles:
        - mysql-role
    ```
 16. check the syntax and execute it
   ```
   ansible-playbook play5.yaml --syntax-check
   ansible-playbook play5.yaml 
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
5. Check if java is installed
   ```
   java --version
   ```
### On Slave2(EC2 - 3)
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
5. Check if java is installed
   ```
   java --version
   ```
### On Slave3(EC2 - 4)
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
5. Check if mysql is installed
   ```
   mysql --version
   ```
### On Slave4(EC2 - 5)
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
5. Check if mysql is installed
   ```
   mysql --version
   ```
