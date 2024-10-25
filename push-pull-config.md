# Pull and Push Configuration Management (IaC)

**Pull Configuration Management**:

- In the pull method, target servers pull their configuration from a central server.
- An agent installed on the target servers periodically checks for updates and applies them.
- **Examples**: Puppet, Chef.

**Push Configuration Management**:

- In the push method, the central server pushes the configuration directly to the target servers.
- This approach is typically more immediate, as the central server initiates the update process.
- **Examples**: Ansible, SaltStack.

### Tools Supporting Push/Pull

- **Pull-based tools**: Puppet, Chef.
- **Push-based tools**: Ansible, SaltStack.

### Terraform's Configuration Method

Terraform primarily uses a **push** configuration model. It directly applies the desired state to the infrastructure by communicating with the cloud provider's API².

### Which is Better: Push or Pull Configuration Management?

The choice between push and pull configuration management depends on your specific needs:

- **Pull**: Better for environments where servers need to be autonomous and can check for updates at regular intervals.
- **Push**: More suitable for environments where immediate updates are necessary and the central server can manage the timing of updates.

Each method has its advantages and can be chosen based on factors like infrastructure size, update frequency, and control requirements.
