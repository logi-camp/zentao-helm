Helm chart for <b>Zentao</b> using `easysoft/quickon-zentao` docker image

Requirements:
- A volume provisioner (like longhorn, openebs ors s3)
- Ingress provider (like haproxy, nginx or traefik)
- Certificate Manager (cert-manager.io)

## Installation using helm command

To add helm repo run the following command

```bash
helm repo add zentao https://github.com/logi-camp/zentao-helm/raw/main/
```

Customize values.yaml file and save to your current dir the run the following command for install the helm chart

```bash
helm install zentao -n <desired-namespace> zentao/zentao --values ./values.yaml
```

## Installation using Ansible

Create `hosts` file with your server credentials and set your master nodes group to `master`

Save the following codes into a file named like `zentao.yaml` then override all item between `<>` chars
then run `ansible-playbook -i hosts ./zentao.yaml`

```yaml
- hosts: master
  become: yes
  tasks:
    - name: Add zentao helm repo
      become: yes
      become_user: kube-admin
      kubernetes.core.helm_repository:
        name: zentao
        repo_url: https://github.com/logi-camp/zentao-helm/raw/main/ 

    - name: Install Zentao
      become: yes
      become_user: kube-admin
      kubernetes.core.helm:
        name: zentao
        chart_ref: zentao/zentao
        release_namespace: zentao
        values:
          zentao:
            domain: <zentao-domain>
            git:
              domain: <git-domain>
              instanceName: Git instance name
              password: '<git-password>'
            smtp:
              host: mail.example.com
              fromName: Zentao
              user: zentao@example.com
              password: '<smtp-password>'
          mysql:
            auth:
              rootPassword: '<mysql-password>'
              password: '<mysql-password>'
```
