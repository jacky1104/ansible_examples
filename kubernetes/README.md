# Prepare

1. make sure all the nodes has the right hostname

hostnamectl set-hostname <host-name>

2.

```markdown
ansible-playbook -i hosts kube-dependencies.yml
```

```markdown
ansible-playbook -i hosts master.yml
```

```markdown
ansible-playbook -i hosts worker.yml
```
