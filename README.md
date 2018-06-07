# ansible-install-helm

先在 inventory/example/group_vars/all.yml 編輯你要的版本

```
helm_version: v2.8.2
helm_url: https://storage.googleapis.com/kubernetes-helm/helm-{{ helm_version }}-linux-amd64.tar.gz
```

inventory/example/hosts.ini 換成你的server

```
tp-lab01 ansible_ssh_host=192.168.2.21
```

### 有外網情況下，直接安裝

```
cp -a inventory/example inventory/your_server
ansible-playbook -i inventory/your_server/hosts.ini -b install.yml
```

```
ansible-playbook -i inventory/your_server/local -e helm_refresh_cache="--skip-refresh" --skip-tags=download -b install.yml
```

### 像是銀行之類環境無外網

inventory/local/hosts.ini 就是localhost，都下載到本機

```
localhost ansible_connection=local ansible_python_interpreter=/usr/local/bin/python
```

下載helm tgz, tiller image到本機

```
ansible-playbook -i inventory/local/hosts.ini -b prepare.yml
```

檔案想辦法抄過去遠端

```
scp -r . remote-server:~/ansible-install-helm
```

在遠端server執行local安裝，我用了python起一個臨時simple http server的技巧

```
ssh remote-server
cd ansible-install-helm
ansible-playbook -i inventory/local/hosts.ini -b local-install.yml
```
