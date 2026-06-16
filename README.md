# Active-Directory-Labs
Enterprise infrastructure simulation featuring Windows Server, Active Directory DS deployment, GPO security baseline enforcement, and user management controls.
Phase 1: Virtual Machines & Network Setup
Step 1: Download and Provision Operating Systems

    Downloaded a Windows Server 2016 instance and a Windows 10 client machine into a virtual environment.

Step 2: Configure Server IP and DNS

Before making the domain, the server needs a fixed network address.

    Logged into the Windows Server 2016.

    Opened the network adapter settings.

    Configured Internet Protocol Version 4 (TCP/IPv4) properties by checking "Use the following IP address" and gave it a unique IP address and subnet mask:

        IP Address: 192.168.10.10

        Subnet Mask: 255.255.255.0

    Set the Preferred DNS Server to 127.0.0.1 (pointing to itself) so the server can handle network name resolution.

    Opened Server Manager, installed Active Directory Domain Services, and created a new domain named genetech.com.

Step 3: Configure Client IP and DNS

For the client to connect to the domain, it must use the server's DNS.

    Logged into the Windows 10 client machine.

    Opened its network adapter properties and selected TCP/IPv4.

    Assigned the client its own unique IP address on the same network segment:

        IP Address: 192.168.10.20

        Subnet Mask: 255.255.255.0

    Crucial Step: Set the client's Preferred DNS Server to the server's IP address (192.168.10.10).

    Opened System Settings on the client, typed in the domain genetech.com, entered the administrator credentials, and successfully connected the client computer to the domain.

Phase 2: Active Directory & Group Policy Objects (GPO)
Step 4: Create Users and Organizational Units (OUs)

    Opened Active Directory Users and Computers.

    Created a new OU (Organizational Unit) to organize the domain resources.

    Created a new domain user account named sarahali.  

    Put the user sarahali and the Windows 10 client computer inside the newly made OU.  

Step 5: Lock Down Command Prompt (CMD) via GPO

    Opened Group Policy Management.

    Created a new GPO (Group Policy Object) and linked it directly to the OU made earlier.

    Edited the policy to restrict user environment execution settings.

    Result: Enforced the GPO so that the user does not have access to CMD. When logging into the client as sarahali, access to the command prompt was completely blocked.

Phase 3: Trend Micro Endpoint Security Deployment
Step 6: Convert and Deploy the Application Package

Active Directory software deployment requires an MSI package.

    Downloaded an endpoint security package from Trend Micro.  

    Opened the package and found that one of the files was an exe file and the other was an msi file.

    Converted the exe file to an msi file using a wrapper utility to make it compatible with Group Policy installation rules.

    Created a software installation policy within the GPO and deployed the application to the user through the GPO, linking it to the OU made earlier.

    Rebooted the machine. After successful deployment, the endpoint was completely visible on the Trend Micro console and actively protecting the system.  

Step 5: Live Malware Testing

    To check the real-world working functionality of Trend Micro, downloaded a virus (a safe test malware sample) on the client machine.  

    Result: The security agent worked perfectly—it instantly detected, isolated the virus, and deleted it.

Phase 4: Compatibility Barriers & Upgrading to Server 2025
Step 6: Server Compatibility Limitation

    Downloaded the advanced Deep Security endpoint protection agent to install it directly on the server layer.

    Result: The installation did not work because Trend Micro does not provide service for Server 2016 architecture on this specific package.

Step 7: Migrate the Domain to Server 2025

To bypass the operating system restriction, the domain environment had to be upgraded.

    Downloaded and set up a new virtual machine running Windows Server 2025.

    Transferred the domain to Server 2025 by promoting it to a Domain Controller and replicating the forest data.

    Did the installation again for the Trend Micro server defense agent on the new Server 2025 system, which worked perfectly this time.

Phase 5: Trend Micro Policies & Active Directory Synchronization
Step 8: Deploy Policies via Command Line Integration

    Created custom security policies directly on the Trend Micro web management dashboard.

    Selected the target machine profiles and deployed them.

    The Barrier: To successfully deploy the configurations, the AD (Active Directory) should also be connected to Trend Micro.

    Downloaded the specialized synchronization package onto the server.

    Opened the CMD (Command Prompt) window as an administrator.

    Navigated the file paths by writing the file location into the prompt.

    Executed the sync utility file by entering -i for installation and -s for sync:
