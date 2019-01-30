Role file tree

```
# tree sos
sos
|-- defaults
|   `-- main.yml
|-- meta
|   `-- main.yml
|-- README.md
|-- tasks
|   `-- main.yml
|-- templates
|   `-- etc
|       `-- redhat-support-tool.conf.j2
|-- tests
|   |-- inventory
|   `-- test.yml
`-- vars
    `-- main.yml
```

Playbook example
```
# cat sos.yml 
---
- name: OpenShift sosreport
  hosts: 
    - nodes
  become: yes

  roles:
    - sos
```

ansible host from Openshift inventory
```
# ansible-playbook -i ocp39-inventory.yml --list-hosts ./sos.yml
playbook: ./sos.yml
  play #1 (nodes): OpenShift sosreport	TAGS: []
    pattern: [u'nodes']
    hosts (10):
      srv-n1.lab
      srv-n2.lab
      srv-n3.lab
      srv-n4.lab
```

redhat-support-tool is used to upload sosreport to access.redhat.com.
Since password is a base64 encoded string using vault is mandatory.
Create a ansible vault in group_vars/all directory and use either:
--ask-vault or vault_password_file in [default] section from ansible.cfg
The following variables are used in role and must be in vault

- vault_redhat_support_tool_user
- vault_redhat_support_tool_password

Ansible run example

Support case number is provided on command line with sosreport_case variable
Each node will upload is sosreport directly to access.redhat.com
If nodes don't have Internet access sosreport could be fetch locally for upload
Take care about local storage for handeling all sosreport if fecth is enable

```
# ansible-playbook -i inventory.yml sos.yml -e sosreport_case=01234567

PLAY [OpenShift sosreport] *******************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************************************************************************************************************************************
ok: [srv-n4.lab]
ok: [srv-n1.lab]
ok: [srv-n3.lab]
ok: [srv-n2.lab]

TASK [sos : ensure support tools are installed] **********************************************************************************************************************************************************************************************
ok: [srv-n4.lab]
ok: [srv-n1.lab]
ok: [srv-n3.lab]
ok: [srv-n2.lab]

TASK [sos : configure redhat-support-tool] ***************************************************************************************************************************************************************************************************
ok: [srv-n4.lab]
ok: [srv-n1.lab]
ok: [srv-n3.lab]
ok: [srv-n2.lab]

TASK [sos : build temp dir path] *************************************************************************************************************************************************************************************************************
ok: [srv-n4.lab]
ok: [srv-n1.lab]
ok: [srv-n3.lab]
ok: [srv-n2.lab]

TASK [sos : create directory in /tmp/sosreport] **********************************************************************************************************************************************************************************************
changed: [srv-n4.lab]
changed: [srv-n1.lab]
changed: [srv-n3.lab]
changed: [srv-n2.lab]

TASK [sos : create sosreport] ****************************************************************************************************************************************************************************************************************
changed: [srv-n4.lab]
changed: [srv-n1.lab]
changed: [srv-n3.lab]
changed: [srv-n2.lab]

TASK [sos : find created sosreport files] ****************************************************************************************************************************************************************************************************
changed: [srv-n4.lab]
changed: [srv-n1.lab]
changed: [srv-n3.lab]
changed: [srv-n2.lab]

TASK [sos : upload sosreport to case 02298132] ***********************************************************************************************************************************************************************************************
changed: [srv-n4.lab] => (item=sosreport-srv-n4-02298132-2019-01-29-tgwseet.tar.xz)
changed: [srv-n4.lab] => (item=sosreport-srv-n4-02298132-2019-01-29-tgwseet.tar.xz.md5)
changed: [srv-n1.lab] => (item=sosreport-srv-n1-02298132-2019-01-29-tndperk.tar.xz)
changed: [srv-n3.lab] => (item=sosreport-srv-n3-02298132-2019-01-29-ezmgxbu.tar.xz)
changed: [srv-n1.lab] => (item=sosreport-srv-n1-02298132-2019-01-29-tndperk.tar.xz.md5)
changed: [srv-n3.lab] => (item=sosreport-srv-n3-02298132-2019-01-29-ezmgxbu.tar.xz.md5)
changed: [srv-n2.lab] => (item=sosreport-srv-n2-02298132-2019-01-29-yerhpbt.tar.xz)
changed: [srv-n2.lab] => (item=sosreport-srv-n2-02298132-2019-01-29-yerhpbt.tar.xz.md5)

TASK [sos : fetch files to ~/sosreport on the controller] ************************************************************************************************************************************************************************************
changed: [srv-n4.lab] => (item=sosreport-srv-n4-02298132-2019-01-29-tgwseet.tar.xz)
changed: [srv-n4.lab] => (item=sosreport-srv-n4-02298132-2019-01-29-tgwseet.tar.xz.md5)
changed: [srv-n1.lab] => (item=sosreport-srv-n1-02298132-2019-01-29-tndperk.tar.xz)
changed: [srv-n3.lab] => (item=sosreport-srv-n3-02298132-2019-01-29-ezmgxbu.tar.xz)
changed: [srv-n1.lab] => (item=sosreport-srv-n1-02298132-2019-01-29-tndperk.tar.xz.md5)
changed: [srv-n3.lab] => (item=sosreport-srv-n3-02298132-2019-01-29-ezmgxbu.tar.xz.md5)
changed: [srv-n2.lab] => (item=sosreport-srv-n2-02298132-2019-01-29-yerhpbt.tar.xz)
changed: [srv-n2.lab] => (item=sosreport-srv-n2-02298132-2019-01-29-yerhpbt.tar.xz.md5)

TASK [sos : remove /tmp/sosreport directory] *************************************************************************************************************************************************************************************************
changed: [srv-n4.lab]
changed: [srv-n1.lab]
changed: [srv-n3.lab]
changed: [srv-n2.lab]

PLAY RECAP ***********************************************************************************************************************************************************************************************************************************
srv-n1.lab      : ok=10   changed=6    unreachable=0    failed=0   
srv-n2.lab      : ok=10   changed=6    unreachable=0    failed=0   
srv-n3.lab      : ok=10   changed=6    unreachable=0    failed=0   
srv-n4.lab      : ok=10   changed=6    unreachable=0    failed=0  
```
