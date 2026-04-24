### Tasks To Be Performed:
1. Create a script which can add text “This text has been added by custom
script” to /tmp/1.txt
2. Run this script using Ansible on all the hosts

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
12. Create Script file script1.sh
    ```
    nano script1.sh
    echo “This text has been added by custom script” > /tmp/1.txt 
    ```
13. Now create a playbook
    ```
    sudo nano play2.yaml
    ```
14. Add below code inside play2.yaml
    ```
    ---
    - name: executing script file inside Slave1 and slave-2
      hosts: slave-1, slave-2
      become: true

      tasks:
          - name: executing script file
            script: script1.sh
    ```
15. check the syntax
    ```
    ansible-playbook play1.yaml --syntax-check
    ```
16. Execute the playbook and once finished check on slave machines
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
5. check if file is created (once master machine's step 16 is completed)
   ```
   cd /tmp
   ls
   sudo nano 1.txt
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
5. check if file is created (once master machine's step 16 is completed)
   ```
   cd /tmp
   ls
   sudo nano 1.txt
   ```
