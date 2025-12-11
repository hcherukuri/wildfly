wildfly app deploy role
========================

Role to deploy applications (.war, .ear, .jar) to WildFly application server in either standalone or domain mode.

Requirements
------------

* WildFly server must be installed and running
* Management interface must be accessible
* The `wildfly_utils` role is required as a dependency

<!--start argument_specs-->
Role Defaults
-------------

| Variable | Description | Default |
|:---------|:------------|:--------|
|`wildfly_app_deploy_file`| Path to the application artifact (.war, .ear, .jar) on the Ansible controller | `/path/to/your/application.war` |
|`wildfly_app_deploy_name`| The name to use for the deployment in WildFly (e.g., app-v1.war) | `application.war` |
|`wildfly_app_deploy_mode`| Deployment mode: 'standalone' or 'domain' | `standalone` |
|`wildfly_app_deploy_server_groups`| List of server groups to deploy to (Required if wildfly_app_deploy_mode is 'domain') | `[]` |
|`wildfly_home`| WildFly installation directory (inherits from wildfly_install role) | Inherited |
|`jboss_home`| WildFly installation directory, for backwards compatibility (inherits from wildfly_utils role) | Inherited |
|`jboss_cli_controller_host`| Hostname for connecting to cli | `localhost` |
|`jboss_cli_controller_port`| Port for connecting to cli | `9990` |
|`wildfly_port_range_offset`| Increment for `jboss.socket.binding.port-offset` | `0` |
|`wildfly_user`| posix user account for wildfly | `wildfly` |
|`wildfly_app_deploy_requires_privilege_escalation`| Whether to use privilege escalation when copying files and executing CLI commands | `True` |
|`wildfly_app_deploy_cli_user`| User for CLI authentication (optional, if management interface requires auth) | `""` |
|`wildfly_app_deploy_cli_password`| Password for CLI authentication (optional, if management interface requires auth) | `""` |

Role Variables
--------------

| Variable | Description | Required |
|:---------|:------------|:---------|
|`wildfly_app_deploy_file`| Path to the application artifact on the Ansible controller | `Yes` |
|`wildfly_app_deploy_server_groups`| List of server groups (required when wildfly_app_deploy_mode is 'domain') | `Yes` (when mode is 'domain') |
<!--end argument_specs-->

## Example Playbooks

### Deploy Application to Standalone Server

Deploy a WAR file to a standalone WildFly instance.

```yaml
- name: Deploy application to WildFly
  hosts: wildfly_servers
  gather_facts: false
  roles:
    - role: middleware_automation.wildfly.wildfly_app_deploy
  vars:
    wildfly_app_deploy_file: "/path/to/myapp.war"
    wildfly_app_deploy_name: "myapp.war"
    wildfly_app_deploy_mode: "standalone"
    wildfly_home: "/opt/wildfly/wildfly-38.0.0.Final/"
    jboss_cli_controller_host: "localhost"
    jboss_cli_controller_port: "9990"
```

### Deploy Application to Domain Mode

Deploy a WAR file to multiple server groups in domain mode.

```yaml
- name: Deploy application to WildFly domain
  hosts: wildfly_domain_controller
  gather_facts: false
  roles:
    - role: middleware_automation.wildfly.wildfly_app_deploy
  vars:
    wildfly_app_deploy_file: "/path/to/myapp.war"
    wildfly_app_deploy_name: "myapp.war"
    wildfly_app_deploy_mode: "domain"
    wildfly_app_deploy_server_groups:
      - main-server-group
      - other-server-group
    wildfly_home: "/opt/wildfly/wildfly-38.0.0.Final/"
    jboss_cli_controller_host: "localhost"
    jboss_cli_controller_port: "9990"
```

### Deploy with Port Offset

Deploy to a WildFly instance using a port offset.

```yaml
- name: Deploy application with port offset
  hosts: wildfly_servers
  gather_facts: false
  roles:
    - role: middleware_automation.wildfly.wildfly_app_deploy
  vars:
    wildfly_app_deploy_file: "/path/to/myapp.war"
    wildfly_app_deploy_name: "myapp.war"
    wildfly_app_deploy_mode: "standalone"
    wildfly_port_range_offset: 100
    jboss_cli_controller_port: "10090"  # 9990 + 100
```

## Dependencies

* `wildfly_utils` - Required for CLI operations

## License

Apache License 2.0

## Author Information

* [Harsha Cherukuri](https://github.com/hcheruku)

