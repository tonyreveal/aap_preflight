# aap_preflight

The playbooks included in this repository are intended to help validate all AAP port connectivity, 
database credentials (when applicable), etc before attempting to install.  Also included is a playbook to do a post-install validation of a cluster to ensure services are all running, required users were created successfully and correctly, umask is 0022 for the AWX user, etc

The pre-install playbook does not cover every possible scenario.  But it does validate that all nodes in the inventory have appropriate connectivity and that any credentials specified for a non-AAP installer managed database are good.

See list of [pre-install checks](#pre-install-checks-performed) below.

See list of [post-install checks](#post-install-checks-performed) below.


## Playbook(s):

| Playbook Name| Description |
|---|---|
| `preinstall_checks.yml` | Uses the AAP install inventory to perform pre-installation validation tasks. |
| `postinstall_tasks.yml` | Uses the AAP install inventory to perform post-installation tasks. |
| `assess_deployment.yml` | Uses the AAP inventory to assess an existing AAP deployment. |


## Other Recommendations:

If you are deploying a large AAP cluster with instance groups and hop nodes in your inventory, I suggest you run `./setup.sh -- --tag generate_dot_file` before you perform the install.  This will generate a file called `mesh-topology.dot`.  You can then copy the contents of the dot file into [GraphViz Online]('https://dreampuf.github.io/GraphvizOnline') to view the topology and make sure it looks correct.



### Usage:

The inventory used must be your AAP install inventory file

`ansible-playbook preinstall_checks.yml -i inventory`


### Pre-install checks performed:

- Installs nmap-ncat (for port connectivity tests)
- Installs python-psutil (for getting ruby / chef PIDs to make sure chef-client is stopped)
- Installs python3.9-psycopg2 (required for database tests)
- Disables appstream repo on all hosts in inventory
- Enables baseos repo on all hosts in inventory
- Check if Chef-Client is installed
- Check if Chef-Client service is running
- Stops and Disables chef-client on all hosts in inventory if installed and running
- AAP install database validation
  - Check all pg_* vars for controller database
  - Check all automationhub_pg_* vars for automation hub
  - Check all automationedacontroller_pg_* vars for eda controller
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


### Post-install checks performed:

- Re-enables repositories on all hosts in inventory
- Ensures AAP repository is disabled (required for patching)
- Re-enables chef-client if installed as a service
  - I really don't like the idea of managing AAP with Chef
- Checks that all appropriate groups are created on controllers, execution, and hop nodes
- Checks that all appropriate users are created and members of the correct groups on controllers, execution, and hop nodes
- Check Hop node receptor port connectivity to Controller.
  - Checks if receptor is installed.
  - Checks if receptor service is running.
  - If receptor is installed and running then this block should successfully report a connection, provided ACLs and firewall ports are set correctly.
  - Returns "no route to host" if port is not open but that could include situations where firewalld port on the controller node isn't enabled or if Receptor isn't already installed.
- Check Execution node receptor port connectivity to Controller if not behind a hop node.
  - Checks if receptor is installed.
  - Checks if receptor service is running.
  - If receptor is installed and running then this block should successfully report a connection, provided ACLs and firewall ports are set correctly.
  - Returns "no route to host" if port is not open but that could include situations where firewalld port on the controller node isn't enabled or if Receptor isn't already installed.