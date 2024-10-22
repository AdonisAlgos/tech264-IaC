# Learning Infrustructure as Code (IaC)

## What is IaC?
Infrastructure as Code (IaC) is the process of managing and provisioning computing infrastructure using machine-readable configuration files instead of manual processes.

![Infrastructure as Code](./images/iac.png)

## Benefits of IaC?

* Consistency and repeatability
* Scalability and faster deployments
* Version control for infrastructure
* Reduced manual errors
* Automated and reliable infrastructure management

## When/where to use IaC?
IaC is used in environments requiring automation, cloud deployments, CI/CD pipelines, and when scaling infrastructure efficiently.

## What are the tools available for IaC?

* Terraform
* AWS CloudFormation
* Ansible
* Puppet
* Chef

## What is configuration management (CM)?
CM refers to the process of standardizing resource configurations to ensure consistency across systems, often automating changes and deployments.

## What is provisioning of infrastructure? Do CM tools do it?
Provisioning is the process of setting up resources such as servers, databases, and networks. Some CM tools (e.g., Ansible, Puppet) can provision infrastructure but are more commonly used for configuring it post-provisioning.

## What is Ansible and how does it work?
Ansible is an open-source automation tool for configuration management, provisioning, and application deployment. It works by connecting to nodes via SSH, executing playbooks (in YAML) that describe the desired state.

## Who is using IaC and Ansible in the industry?
Major companies like Netflix, Capital One, NASA, and Google use IaC and tools like Ansible for automating infrastructure management and deployments.