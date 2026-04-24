### Tasks To Be Performed:
1. Setup Ansible cluster with 3 nodes
2. On slave 1 install Java
3. On slave 2 install MySQL server
4. Do the above tasks using Ansible Playbooks

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
12. Now create a playbook
    ```
    sudo nano play1.yaml
    ```
13. Add below code inside play1.yaml
    ```
    ---
    - name: Install Java on Slave1
      hosts: slave-1
      become: true

      tasks:
          - name: Install OpenJDK
            apt:
              name: openjdk-17-jdk
              state: latest
    
    - name: Install mysql on Slave2
      hosts: slave-2
      become: true

      tasks:
          - name: Install mysql
            apt:
              name: mysql-server
              state: latest
    ```
14. check the syntax
    ```
    ansible-playbook play1.yaml --syntax-check
    ```
15. Execute the playbook and once finished check on slave machines
    ```
    ansible-playbook play1.yaml
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
5. check java version (once master machine's step 15 is completed)
6. ```
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
5. check java version (once master machine's step 15 is completed)
6. ```
   mysql --version
   ```
