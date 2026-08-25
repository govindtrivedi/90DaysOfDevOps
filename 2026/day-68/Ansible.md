Day 68 — Short Answer

Task 1 — Ansible:
Learned that Ansible is an agentless configuration-management tool that uses SSH to manage servers. The control node sends modules/tasks to managed nodes through the inventory.

Task 2 — Lab:
Set up 3 EC2 instances for web, app, and database servers with SSH access.

Task 3 — Installation:
Installed Ansible on the control node and verified it with ansible --version.

Task 4 — Inventory:
Created inventory.ini with separate web, app, and db groups and verified connectivity using:

ansible all -m ping

All servers returned SUCCESS / pong.

Task 5 — Ad-hoc Commands:
Used Ansible to check uptime, memory, disk space, install packages, and copy files. --become provides sudo/root privileges when required.

Task 6 — Groups & Patterns:
Created parent groups and used patterns such as web:app and all:!db to target specific servers.

Key takeaway: Ansible lets me manage multiple servers from one control node without installing agents, making configuration consistent and repeatable.
