ansible-k8s-install
=========

Rol de Ansible que instala Kubernetes en un máster y N nodos. Se han extraído tareas del rol https://github.com/geerlingguy/ansible-role-kubernetes y adaptado para un proyecto específico sobre Raspberrys Pis, y además con pruebas con Molecule, Vagrant y VirtualBox desde el playbook ansible-tfm-cpg.

Requirements
------------

Este rol ha sido testeado sobre Ubuntu-20.10

Role Variables
--------------

## Variables del rol
Las siguientes variables son asignadas a nivel de rol:
```
procps_package: procps
kubelet_environment_file_path: /etc/default/kubelet
```

## Variables por defecto
Las siguientes variables son asignadas por defecto y puede modificarse su valor a la hora de llamar al rol. Observamos que el sistema de red entre pods por defecto es Flannel, y es posible elegir calico si se desea. 
```
kubernetes_packages:
  - name: kubelet
    state: present
  - name: kubectl
    state: present
  - name: kubeadm
    state: present
  - name: kubernetes-cni
    state: present

kubernetes_version: '1.19'
kubernetes_kubelet_extra_args: ""
kubernetes_kubeadm_init_extra_opts: ""
kubernetes_join_command_extra_opts: ""

kubernetes_allow_pods_on_master: false
kubernetes_enable_web_ui: true
kubernetes_web_ui_manifest_file: https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml

kubernetes_pod_network:
  # Flannel CNI.
  cni: 'flannel'
  cidr: '10.244.0.0/16'
```
## Variables para pruebas con molecule
Se aconseja establecer estas variables para pruebas con molecule al llamar a este rol:
```
kubernetes_kubelet_extra_args: "--fail-swap-on=false --cgroup-driver=cgroupfs"
kubernetes_apiserver_advertise_address: "{{ ansible_enp0s8.ipv4.address }}"
```


Dependencias
------------

Este rol cuenta en sus dependencias con el rol **geerlingguy.docker** el cual es descargado desde Gallaxy mediante su mención en el fichero meta/main.yml de este rol.

El rol en cuestión instala Docker en todas las máquinas del clúster. Las variables necesarias para su despliegue son:

```
docker_users: [usuario1,usuario2,vagrant]
Se pueden poner varios usuarios. Todos ellos podrán ejecutar el comando docker para operar con contenedores e imágenes.
Si estamos haciendo pruebas con molecule/vagrant el usuario será vagrant.

docker_install_compose:
Valores posibles false o true, indica si se instala docker compose

docker_service_state:
Estado del servicio una vez despleguemos el rol (started,stopped)

docker_service_enabled:
Valores posibles false o true si queremos que el servicio esté habilitado en el arranque.

docker_restart_handler_state:
Estado del servicio cuando se llame al handler, idealmente restarted para que se reinicie cuando hagamos cambios.

```


Example Playbook
----------------

El rol se ejecuta sobre todos las máquinas (all) y será en función de la variable kubernetes\_role si se aplican tareas de master o de node: 
    - hosts: all
      roles:
         - role: "ansible-k8s-install"

License
-------

BSD

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
