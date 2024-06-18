# aap_preflight

One common issue with installing Ansible Automation Platform is running into errors 15 minutes into the installation.  Only to have to make corrections, re-run setup.sh and wait another 15 minutes to find out if your changes corrected the problem or not.

The pre-install checks playbook does not cover every possible scenario.  But it does validate that all nodes in the inventory have appropriate connectivity and that any credentials specified for a non-AAP installer managed database are good.

## Playbook(s):

| Playbook Name| Description |
|---|---|
| `preinstall_checks.yml` | Uses the AAP install inventory to perform pre-installation validation tasks. |
| `postinstall_tasks.yml` | Uses the AAP install inventory to perform post-installation tasks. |
