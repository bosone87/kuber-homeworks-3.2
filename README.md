# Домашнее задание к занятию «Установка Kubernetes»

### Цель задания

Установить кластер K8s.

### Чеклист готовности к домашнему заданию

1. Развёрнутые ВМ с ОС Ubuntu 20.04-lts.


### Инструменты и дополнительные материалы, которые пригодятся для выполнения задания

1. [Инструкция по установке kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/).
2. [Документация kubespray](https://kubespray.io/).

-----

### Задание 1. Установить кластер k8s с 1 master node

1. Подготовка работы кластера из 5 нод: 1 мастер и 4 рабочие ноды.
2. В качестве CRI — containerd.
3. Запуск etcd производить на мастере.
4. Способ установки выбрать самостоятельно.

### Кластер k8s с 1 master node

1. Развернём инфраструктуру кластера с помощью terraform на YC, получим Inventory-файл.
<p align="center">
    <img width="1200 height="600" src="/img/terraform_inv.png">
</p>

2. Установим необходимое ПО и настройки, плейбуком ansible.
[Playbook](playbook.yml)

<p align="center">
    <img width="1200 height="600" src="/img/ansible_playbook.png">
</p>

3. Добавим в кластер worker-node-0
<p align="center">
    <img width="1200 height="600" src="/img/kubeadm_join_w0.png">
</p>

4. Добавим в кластер worker-node-1
<p align="center">
    <img width="1200 height="600" src="/img/kubeadm_join_w1.png">
</p>

5. Добавим в кластер worker-node-2
<p align="center">
    <img width="1200 height="600" src="/img/kubeadm_join_w2.png">
</p>

6. Добавим в кластер worker-node-3
<p align="center">
    <img width="1200 height="600" src="/img/kubeadm_join_w3.png">
</p>

7. Kube config на мастер ноде
<p align="center">
    <img width="1200 height="600" src="/img/kube_config_m.png">
</p>

8. Устаналиваем flannel и проверяем статус нод
<p align="center">
    <img width="1200 height="600" src="/img/kubectl_get_p_flannel.png">
</p>

9. Запуск etcd производим на мастере
<p align="center">
    <img width="1200 height="600" src="/img/kubectl_get_p_wide.png">
</p>


## Дополнительные задания (со звёздочкой)

**Настоятельно рекомендуем выполнять все задания под звёздочкой.** Их выполнение поможет глубже разобраться в материале.   
Задания под звёздочкой необязательные к выполнению и не повлияют на получение зачёта по этому домашнему заданию. 

------
### Задание 2*. Установить HA кластер

1. Установить кластер в режиме HA.
2. Использовать нечётное количество Master-node.
3. Для cluster ip использовать keepalived или другой способ.

### Установим HA кластер

1. Развернём инфраструктуру HA-кластера с помощью terraform на YC, получим Inventory-файл.
<p align="center">
    <img width="1200 height="600" src="/img/ha_terraform_init.png">
</p>

<p align="center">
    <img width="1200 height="600" src="/img/ha_nodes_yc.png">
</p>

2. Установим необходимое ПО и настройки, плейбуком ansible.
[Playbook](HA-playbook.yml)

<p align="center">
    <img width="1200 height="600" src="/img/ha_ansible_playbook.png">
</p>

3. Конфиг keepalived
<p align="center">
    <img width="1200 height="600" src="/img/ha_keepalived_conf_m.png">
</p>

4. Рестарт keepalived
<p align="center">
    <img width="1200 height="600" src="/img/ha_keepalived_restart.png">
</p>

5. Virtual IP добавился на eth0
<p align="center">
    <img width="1200 height="600" src="/img/ha_hosts_eth0.png">
</p>

6. В качестве балансирощика настроим HA-proxy.
<p align="center">
    <img width="1200 height="600" src="/img/ha_haproxy_conf.png">
</p>

7. Рестарт HA-proxy
<p align="center">
    <img width="1200 height="600" src="/img/ha_proxy_restart.png">
</p>

8. Инициализируем control-plane на первой мастер ноде.
<p align="center">
    <img width="1200 height="600" src="/img/ha_cp_init.png">
</p>

9. Добавим вторую мастер ноду в control-plane.
<p align="center">
    <img width="1200 height="600" src="/img/ha_cp_m_add.png">
</p>

10. Добавим третью мастер ноду в control-plane.
<p align="center">
    <img width="1200 height="600" src="/img/ha_cp_m_add1.png">
</p>

11. На каждой мастер ноде в control-plane добавим конфиг.
```yaml
        mkdir -p $HOME/.kube
        sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
        sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

12. Установим flannel на первой ноде
<p align="center">
    <img width="1200 height="600" src="/img/ha_flannel.png">
</p>

13. Проверяем ноды на первом мастере.
<p align="center">
    <img width="1200 height="600" src="/img/ha_kubectl_get_n_m1.png">
</p>

14. Проверяем ноды на втором мастере.
<p align="center">
    <img width="1200 height="600" src="/img/ha_kubectl_get_n_m2.png">
</p>

15. Проверяем ноды на третьем мастере.
<p align="center">
    <img width="1200 height="600" src="/img/ha_kubectl_get_n_m3.png">
</p>

16. НО не удалось разобраться почему, в данный кластер, не получается присоединить worker ноды. При добавлении worker ноды, виснет в статусе - [preflight] Running pre-flight.


| UPD |
| :---: |
```
Создаю три мастер-ноды: 10.0.1.16, 10.0.1.30, 10.0.1.34. Keepalived поднимает ip кластера - 10.0.1.100, который явлеется Secondary IP на eth0 каждой мастер-ноде.
```

**На первом управляющем узле (10.0.1.16) выполняем:**
```
kubeadm init \
               --pod-network-cidr=10.0.1.0/24 \
               --control-plane-endpoint "10.0.1.100:8443" \
               --upload-certs
```

<p align="center">
    <img width="1200 height="600" src="/img/upd_ha_mn1_kubeadm_init.png">
</p>

**На втором и третьем управляющих узлах выполняем добавление к control-plane:**
```
kubeadm join 10.0.1.100:8443 --token bkdamw.x6a710qf92vcxokw \
    --discovery-token-ca-cert-hash sha256:e772a2cb8b1c33139c2538fa8f6f4570403685fe6109a300d49ccea67d779619 \
    --control-plane \ 
    --certificate-key 8f5923ca0ab5078cc48cbc04a01ea8a988c1c13e473ed9bdc4664738006647d2
```

<p align="center">
    <img width="1200 height="600" src="/img/upd_ha_mn2_add.png">
</p>

<p align="center">
    <img width="1200 height="600" src="/img/upd_ha_mn3_add.png">
</p>

**Подключаем воркер-ноду к адресу control-plane:**
```
kubeadm join 10.0.1.100:8443 --token bkdamw.x6a710qf92vcxokw \
    --discovery-token-ca-cert-hash sha256:e772a2cb8b1c33139c2538fa8f6f4570403685fe6109a300d49ccea67d779619
```

<p align="center">
    <img width="1200 height="600" src="/img/upd_wn1_add.png">
</p>

<p align="center">
    <img width="1200 height="600" src="/img/upd_wn1_add_err.png">
</p>

**Не получается присоединить worker ноды. При добавлении worker ноды, виснет в статусе - [preflight] Running pre-flight**


| UPD |
| :---: |
```
Создал HA-кластер на keepalived и haproxy, 3 мастер ноды, 2 воркера.
Воркер-ноды удалось подключить, после настройки статического маршрута в vpc yandex cloud. 
```
**ВМ Yandex Cloud**
<p align="center">
    <img width="1200 height="600" src="/img/upd2_ha_vm_yc.png">
</p>

**Playbook**
<p align="center">
    <img width="1200 height="600" src="/img/upd2_ha_ansible_play.png">
</p>

**Kubeadm init**
<p align="center">
    <img width="1200 height="600" src="/img/upd2_ha_kubeadm_init.png">
</p>

**Add master-cp-0**
<p align="center">
    <img width="1200 height="600" src="/img/upd2_ha_m2_add.png">
</p>

**Add master-cp-1**
<p align="center">
    <img width="1200 height="600" src="/img/upd2_ha_m3_add.png">
</p>

**Add master-cp-2**
<p align="center">
    <img width="1200 height="600" src="/img/upd2_ha_w1_add.png">
</p>

**Add static route**
<p align="center">
    <img width="1200 height="600" src="/img/upd2_ha_yc_vpc_static_route.png">
</p>

**Add worker-0**
<p align="center">
    <img width="1200 height="600" src="/img/upd2_ha_w1_add.png">
</p>

**Add worker-1**
<p align="center">
    <img width="1200 height="600" src="/img/upd2_ha_w2_add.png">
</p>

**Kubectl get nodes**
<p align="center">
    <img width="1200 height="600" src="/img/upd2_ha_get_nodes.png">
</p>


### Правила приёма работы

1. Домашняя работа оформляется в своем Git-репозитории в файле README.md. Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.
2. Файл README.md должен содержать скриншоты вывода необходимых команд `kubectl get nodes`, а также скриншоты результатов.
3. Репозиторий должен содержать тексты манифестов или ссылки на них в файле README.md.
