Day 38 – YAML
Task 1: person.yaml
name: Govind
role: DevOps Learner
experience_years: 0
learning: true
Task 2: Lists
tools:
  - Linux
  - Git
  - Docker
  - Python
  - Kubernetes

hobbies: [reading, coding, music]

Two list formats: Block list (- item) and inline list ([item1, item2]).

Task 3: server.yaml
server:
  name: dev-server
  ip: 192.168.1.10
  port: 8080

database:
  host: localhost
  name: devdb
  credentials:
    user: admin
    password: secret

Tabs: YAML does not allow tabs for indentation; validation gives an indentation/scanning error.

Task 4: Multi-line Strings

Using |:

startup_script: |
  echo "Starting server"
  systemctl start nginx
  echo "Done"

Using >:

description: >
  This is a long description
  written across multiple lines.
| → preserves newlines.
> → folds lines into a single paragraph.
Task 5: Validate
yamllint person.yaml
yamllint server.yaml

Incorrect indentation produces a YAML syntax/indentation error.

Task 6: Spot the Difference

Block 2 is broken because the list indentation is inconsistent:

tools:
- docker
  - kubernetes

Correct:

tools:
  - docker
  - kubernetes

Key takeaway: YAML depends heavily on consistent spaces and indentation; tabs should never be used.
