Installing ansible 
```
sudo apt-add-repository ppa:ansible/ansible
sudo apt update
sudo apt install ansible
```
Correct /etc/ansible/hosts for add ip 
```
sudo vim /etc/ansible/hosts
```
/etc/ansible/hosts    

```
[servers]
server1 ansible_host=172.16.9.101
server2 ansible_host=172.16.9.102
server3 ansible_host=172.16.9.103

[all:vars]
ansible_python_interpreter=/usr/bin/python3
```

check your inventory
```
ansible-inventory --list -y     
```
test connection
```
ansible all -m ping -u alikhan
ssh-copy-id alikhan@10.70.8.45
```

Установка kafka через ansible-playbook


```
- name: Installing Kafka on Ubuntu
  hosts: kafka-servers
  remote_user: alikhan
  become: yes
  vars: 
      installation_dir : /opt/kafka
  tasks:
    - name: Install JRE after apt update
      become: yes
      apt:
        name: 
          - openjdk-17-jre
        state: present
        update_cache: yes
    - name: download acl
      become: yes
      apt:
        name:
          - acl

    - name: Create a group 
      become: yes
      group:
        name: kafka
        state: present

    - name: Create an user 
      become: yes
      user:
        name: kafka
        state: present
        group: kafka

    - name: Create a Directory /opt/kafka
      become: yes
      file:
        path: "{{installation_dir}}"
        state: directory
        mode: 0755
        owner: kafka
        group: kafka

    - name: Download Kafka and Unzip 
      become: yes
      become_user: kafka
      unarchive:
        src: https://downloads.apache.org/kafka/3.8.0/kafka_2.13-3.8.0.tgz
        dest: "{{installation_dir}}"
        mode: 0755
        remote_src: yes

    - name: Move all the files to parent Directory
      become: yes
      become_user: kafka
      shell:
        mv {{installation_dir}}/kafka_2.13-3.8.0/* {{installation_dir}}/.

    - name: Update the log path
      become: yes
      become_user: kafka
      replace:
        path: "{{installation_dir}}/config/server.properties"
        regexp: 'log.dirs=(.+)'
        replace: 'log.dirs={{installation_dir}}/logs'
        backup: yes

    - name: Update the Java Heap Size for Kafka
      become: yes
      become_user: kafka
      replace:
        path: "{{installation_dir}}/bin/kafka-server-start.sh"
        regexp: 'export KAFKA_HEAP_OPTS=(".+")'
        replace: 'export KAFKA_HEAP_OPTS="-Xmx520M -Xms520M"'
        backup: yes
    

    - name: Create a Service file for ZooKeeper with Copy module
      become: yes
      copy:
        dest: /etc/systemd/system/zookeeper.service
        content: |
          [Unit]
          Requires=network.target remote-fs.target
          After=network.target remote-fs.target

          [Service]
          Type=simple
          User=kafka
          ExecStart={{installation_dir}}/bin/zookeeper-server-start.sh {{installation_dir}}/config/zookeeper.properties
          ExecStop={{installation_dir}}/bin/zookeeper-server-stop.sh
          Restart=on-abnormal

          [Install]
          WantedBy=multi-user.target
        mode: 0755
      
    - name: Create a Service file for Kafka with Copy module
      become: yes
      copy:
        dest: /etc/systemd/system/kafka.service
        content: |
          [Unit]
          Requires=zookeeper.service
          After=zookeeper.service

          [Service]
          Type=simple
          User=kafka
          ExecStart=/bin/sh -c '{{installation_dir}}/bin/kafka-server-start.sh {{installation_dir}}/config/server.properties > {{installation_dir}}/kafkaservice.log 2>&1'
          ExecStop={{installation_dir}}/bin/kafka-server-stop.sh
          Restart=on-abnormal

          [Install]
          WantedBy=multi-user.target
        mode: 0755

    - name: Start Services
      tags: startservices
      become: yes
      systemd:
        name: '{{item}}'
        state: started
        enabled: yes
      with_items:
        - "kafka"
        - "zookeeper"
    
    - name: Validating if zookeeper is up and listening on port 2181
      wait_for:
        host: localhost
        port: 2181
        delay: 10
        timeout: 30
        state: started
        msg: "Zookeeper not seem to be running"

    - name: Validating if Kafka is up and listening on port 2181
      wait_for:
        host: localhost
        port: 2182
        delay: 10
        timeout: 30
        state: started
```

Установка kafka dashboard 

```
- name: Install and configure Kafka-UI
  hosts: kafka-servers
  remote_user: alikhan
  become: yes
  vars:
    kafka_ui_version: "0.7.2"
    installation_dir: /opt/kafka-ui
    service_file: /etc/systemd/system/kafka-ui.service
  tasks:
    - name: Create a directory for Kafka-UI
      file:
        path: "{{ installation_dir }}"
        state: directory
        mode: '0755'

    - name: Download Kafka-UI JAR file
      get_url:
        url: "https://github.com/provectus/kafka-ui/releases/download/v{{ kafka_ui_version }}/kafka-ui-api-v{{ kafka_ui_version }}.jar"
        dest: "{{ installation_dir }}/kafka-ui-api.jar"
        mode: '0755'

    - name: Create a systemd service file for Kafka-UI
      copy:
        dest: "{{ service_file }}"
        content: |
          [Unit]
          Description=Kafka UI Service
          After=network.target

          [Service]
          User=root
          ExecStart=/usr/bin/java --add-opens java.rmi/javax.rmi.ssl=ALL-UNNAMED -jar {{ installation_dir }}/kafka-ui-api.jar  --server.port=9092
          Restart=always

          [Install]
          WantedBy=multi-user.target

    - name: Reload systemd daemon
      systemd:
        daemon_reload: yes

    - name: Enable and start Kafka-UI service
      systemd:
        name: kafka-ui
        state: started
        enabled: yes

```

dashboard 8080
broker 9092