# Prepare

1. make sure all the nodes has the right hostname

hostnamectl set-hostname <host-name>

2.make sure using containerd as container runtime
```markdown
systemctl stop kubelet
systemctl disable docker.service --now
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml
sudo systemctl restart containerd

In master node:
kubectl cordon <node-name>
kubectl drain <node-name> --ignore-daemonsets

In worker node:
vi /var/lib/kubelet/kubeadm-flags.env
--container-runtime=remote  --container-runtime-endpoint=unix:///run/containerd/containerd.sock


In Master node:
kubectl edit node <node-name>
Change the value of kubeadm.alpha.kubernetes.io/cri-socket from /var/run/dockershim.sock to the CRI socket path of your choice (for example unix:///run/containerd/containerd.sock



```
3.make sure using cgroupfs as cgroup driver, because kubelet default using cgroupfs
```markdown
How to change to systemd?
Try to update some config, but not working.
```

4.make sure using flannel as network driver
5. 

```markdown
ansible-playbook -i hosts kube-dependencies.yml
```

```markdown
ansible-playbook -i hosts master.yml
```

```markdown
ansible-playbook -i hosts worker.yml
```
