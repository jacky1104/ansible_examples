```markdown
ansible-playbook -i hosts  auth_key.yml   -u root -k
```



| There is one more problem. If you access to a host via ssh for the first time, you will be asked about whether to add RSA key fingerprint of this host.
However, with ask-pass being specified, ansible will directly run into an error if this is the first time you access to that host.
To walk through this, you will need to disable SSH authenticity checking by adding an ansible.cfg to the place where you want to execute the playbook:


```markdown
[defaults]
host_key_checking = False
```

You can also put this file in the home directory as ~/.ansible.cfg.
