# This is an Ansible repository for k8s control and workers

# What this plyabook contains below(# If you don't need all modules, please comment out unnecessary ones)
0. access_test
1. common(yum_update,hostname,firewalld,selinux,timezone,tools,kernel,file_descriptor,history_date,postfix) 
2. k8s
3. kubeadm_init (if you need

# How to run
(Before running middleware playbooks, You should run access_test playbook to check the target server is correct)
1. Copy private key to the keys/user.key
2. Modify group_vars/all.yml
3. Move to the directory where playbook.yml locates
4. Dry run command {{ ansible-playbook --private-key keys/user.key -i inventory/hosts.ini -l control playbooks/control.yml -C }} for control plane
5. Run command {{ ansible-playbook --private-key keys/user.key -i inventory/hosts.ini -l control playbooks/control.yml }} for control plane
6. Dry run command {{ ansible-playbook --private-key keys/user.key -i inventory/hosts.ini -l workers playbooks/worker.yml -C }} for workers
7. Run command {{ ansible-playbook --private-key keys/user.key -i inventory/hosts.ini -l workers playbooks/worker.yml }} for workers

# Caution!!
You need create a user for exectuing Ansible on AlamLinux9 if you use vagrant machine

# How to create an ansible exec user
1. useradd ansible-user
2. vi /etc/sudoers.d/ansible-user
3. ansible-user ALL=(ALL) NOPASSWD:ALL
4. su - ansible-user
5. cd ~
6. ssh-keygen -t rsa -b 2048
7. cd ~/.ssh
8. ssh-keygen -p -m PEM -f id_rsa
9. cat ./id_rsa.pub > ./authorized_keys
10. chmod 600 ./authorized_keys

# After finishing ansible, you need to run commmands to make k8s work
1. deploy flannel → kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml
2. make workers join the control-plane → kubeadm join 192.168.33.88:6443 --token {TOKENID} --discovery-token-ca-cert-hash {HASH_ID}