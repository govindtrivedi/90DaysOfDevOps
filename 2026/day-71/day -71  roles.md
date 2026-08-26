I’ve reviewed the Day 71 assignment. It covers **Ansible Roles, Jinja2 Templates, Ansible Galaxy, and Ansible Vault**, with the final goal of combining all four into a reusable deployment structure. 

The assignment’s main workflow is:

1. **Jinja2 templates** → generate dynamic Nginx configuration.
2. **Ansible Roles** → organize Nginx tasks, handlers, templates, defaults, and files.
3. **Ansible Galaxy** → install and manage community roles such as `geerlingguy.docker`.
4. **Ansible Vault** → encrypt database passwords/API keys.
5. **Combine everything** → create a complete `site.yml`.
6. **Document the work** in `day-71-roles-templates-vault.md`. 

### Key answers you need to document

**`defaults/main.yml` vs `vars/main.yml`**

* `defaults/main.yml` contains **low-priority default variables**.
* These are intended to be easily overridden by inventory, playbooks, or callers of the role.
* `vars/main.yml` contains **higher-priority role variables** and should be used for values that generally should not be overridden. 

**Why use `requirements.yml`?**

Instead of installing Galaxy roles individually, `requirements.yml` lets you define all required roles and their versions in one reproducible file. You can then install everything with:

```bash
ansible-galaxy install -r requirements.yml
```

The supplied example pins `geerlingguy.docker` to version `7.4.1`, which makes the dependency setup more predictable. 

**Why `--vault-password-file` is better for CI/CD**

`--ask-vault-pass` is interactive, so a pipeline cannot conveniently stop and wait for someone to type a password. A password file allows Ansible to obtain the Vault password non-interactively:

```bash
ansible-playbook db-setup.yml --vault-password-file .vault_pass
```

The assignment also requires `.vault_pass` to have restrictive permissions and to be excluded from Git. 

### Important structure to build

```text
2026/day-71/
├── day-71-roles-templates-vault.md
├── inventory
├── ansible.cfg
├── site.yml
├── template-demo.yml
├── docker-setup.yml
├── db-setup.yml
├── requirements.yml
├── templates/
│   ├── nginx-vhost.conf.j2
│   └── db-config.j2
├── roles/
│   └── webserver/
│       ├── defaults/
│       │   └── main.yml
│       ├── handlers/
│       │   └── main.yml
│       ├── tasks/
│       │   └── main.yml
│       ├── templates/
│       │   ├── nginx.conf.j2
│       │   ├── vhost.conf.j2
│       │   └── index.html.j2
│       ├── files/
│       ├── vars/
│       │   └── main.yml
│       └── meta/
│           └── main.yml
└── group_vars/
    └── db/
        └── vault.yml
```

That structure follows the role layout specified in the assignment. 

One especially important point: **do not use the example secrets from the assignment as real credentials**. They are only demonstration values. In a real project, generate your own secrets and keep the Vault password itself outside Git/your repository.

The final documentation is expected to include the role structure, templates and rendered output, successful execution evidence, Galaxy role usage, the Vault workflow, and when to use **roles vs playbooks vs ad-hoc commands**. 
