Inventory

Ansible’s windows support relies on a few standard variables to indicate the username, password, and connection type (windows) of the remote hosts. These variables are most easily set up in inventory. This is used instead of SSH-keys or passwords as normally fed into Ansible:

[windows]
winserver1.example.com
winserver2.example.com

In group_vars/windows.yml, define the following inventory variables:

# it is suggested that these be encrypted with ansible-vault:
# ansible-vault edit group_vars/windows.yml

ansible_ssh_user: Administrator
ansible_ssh_pass: SecretPasswordGoesHere
ansible_ssh_port: 5986
ansible_connection: winrm

Notice that the ssh_port is not actually for SSH, but this is a holdover variable name from how Ansible is mostly an SSH-oriented system. Again, Windows management will not happen over SSH.

If you have installed the kerberos module and ansible_ssh_user contains @ (e.g. username@realm), Ansible will first attempt Kerberos authentication. This method uses the principal you are authenticated to Kerberos with on the control machine and not ``ansible_ssh_user``. If that fails, either because you are not signed into Kerberos on the control machine or because the corresponding domain account on the remote host is not available, then Ansible will fall back to “plain” username/password authentication.

When using your playbook, don’t forget to specify –ask-vault-pass to provide the password to unlock the file.

Test your configuration like so, by trying to contact your Windows nodes. Note this is not an ICMP ping, but tests the Ansible communication channel that leverages Windows remoting:

ansible windows [-i inventory] -m win_ping --ask-vault-pass

If you haven’t done anything to prep your systems yet, this won’t work yet. This is covered in a later section about how to enable PowerShell remoting - and if necessary - how to upgrade PowerShell to a version that is 3 or higher.

You’ll run this command again later though, to make sure everything is working.
