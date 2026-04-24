### Tasks To Be Performed:
1. Use the previous deployment of Ansible cluster
2. Configure the files folder in the role with index.html
   which should be replaced with the original index.html
4. All of the above should only happen on the slave which
   has NGINX installed using the role

## Solution(based on 3rd assignment)

### On Master(EC2 - 1)
1. ```
   cd nginx-role/
   cd files/
   sudo nano index.html

   <h1>Hello</h1>

   cd ..
   cd tasks/
   sudo nano copy.yaml

   ---
    -name: replacing nginx dashboard
     copy: src=/etc/ansible/roles/nginx-role/files/index.html dest= /var/www/html/

   sudo nano main.yml

   ---
    - include_tasks: copy.yaml

   cd ../../..
   sudo nano play4.yaml
   
   ---
    - name: Replacing nginx dashboard
      hosts: slave-2
      become: true
      roles: 
       - nginx-role

   ansible-playbook play4.yaml --syntax-check
   ansible-playbook play4.yaml
   ```

### On Slave2(EC2 - 3)
check if nginx dashboard is replaced by custom file which we have created
