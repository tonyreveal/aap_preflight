# aap_preflight

One common issue with installing Ansible Automation Platform is running into errors 15 minutes into the installation.  Only to have to make corrections, re-run setup.sh and wait another 15 minutes to find out if your changes corrected the problem or not.

The pre-install checks playbook does not cover every possible scenario.  But it does validate that all nodes in the inventory have appropriate connectivity and that any credentials specified for a non-AAP installer managed database are good.

## Playbook(s):

| Playbook Name| Description |
|---|---|
| `preinstall_checks.yml` | Uses the AAP install inventory to perform pre-installation validation tasks. |
| `postinstall_tasks.yml` | Uses the AAP install inventory to perform post-installation tasks. |


### Pre-install checks performed:

- Installs nmap-ncat (for port connectivity tests)
- Disables appstream repo
- Enables baseos repo
- Disables chef-client if installed and running
- Non-AAP install managed database tests
  - Controller:
    - Port connectivity test using nc
    - Database credential test (attempt to login)
  - Automation Hub:
    - Port connectivity test using nc
    - Database credential test (attempt to login)
    - Install hstore extension
  - EDA Controller:
    - Port connectivity test using nc
    - Database credential test (attempt to login)
- Check if Automation Hub is clustered
  - Check all all nodes have /var/lib/pulp mounted
  - Verify `automationhub_main_url` is set
- Check receptor port connectivity.
  - These tests are really only valuable if receptor is already installed.
  - Checks if receptor is installed.
  - Checks if receptor service is running.
  - If receptor is installed and running then this block should successfully report a connection, provided ACLs and firewall ports are set correctly.
  - Returns "no route to host" if port is not open but that could include the firewalld port on the controller node or if Receptor isn't already installed.
