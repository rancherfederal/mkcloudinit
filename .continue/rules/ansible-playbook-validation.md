---
description: This rule should be applied manually when changes are made to the
  code. It ensures that the Ansible playbook is validated and any errors are
  used to improve the code.
alwaysApply: false
---

Run the command `ansible-playbook generate_cloudinit.yml` after every change. Use the output, especially any errors, to rewrite the code.