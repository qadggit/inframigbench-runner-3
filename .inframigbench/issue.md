# Azure Arc role does not support onboarding Windows machines

## Description

I am attempting to use the `azure_arc` role to onboard a mix of Linux and Windows servers to Azure Arc. The role works correctly for my Linux hosts, but when I target a Windows Server (specifically Windows Server 2022), the role does not perform the necessary steps to install the Azure Connected Machine agent or connect the machine to Azure.

It appears the role currently only contains logic for Linux-based systems and lacks the necessary tasks to handle Windows-specific installation paths (like `AzureConnectedMachineAgent.msi`) and execution methods (PowerShell/WinRM).

## Reproduction Script
```yaml
---
- name: Onboard servers to Azure Arc
  hosts: windows_servers
  vars:
    azure_arc_resource_group: "my-arc-rg"
    azure_arc_subscription_id: "xxxx-xxxx-xxxx"
    azure_arc_tenant_id: "yyyy-yyyy-yyyy"
    azure_arc_location: "eastus"
    azure_arc_access_token: "{{ lookup('env', 'AZURE_ACCESS_TOKEN') }}"
  
  roles:
    - azure_arc
```

## Actual Behavior

When running the playbook against a Windows host, the role skips the installation and connection steps because the condition `ansible_system == 'Linux'` evaluates to false. No agent is installed, and the machine remains unconnected to Azure Arc.

## Expected Behavior

The role should detect that the target host is running Windows (e.g., via `ansible_os_family == 'Windows'`) and automatically perform the following:
1. Download the Azure Connected Machine agent installer (MSI).
2. Install the agent silently.
3. Configure the proxy (if specified).
4. Execute the connect command using the Windows executable to onboard the server to Azure Arc.
