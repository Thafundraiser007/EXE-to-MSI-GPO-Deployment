# Native MSI Deployment Through Group Policy

This section documents the first stage of the lab: deploying a vendor-provided **7-Zip MSI** automatically to a domain-joined Windows 10 client through Group Policy.

[Back to main README](../README.md)

## 1. Windows 10 test client

The Windows 10 VM is the domain client that will receive the software package.

![Windows 10 client](../images/native-msi-gpo/01-windows-10-client-vm.png)

## 2. Windows Server 2022

The server hosts Active Directory, Group Policy Management, and the software distribution share.

![Windows Server dashboard](../images/native-msi-gpo/02-windows-server-2022-dashboard.png)

## 3. Prepare the software repository

The 7-Zip MSI was placed in the server's shared `Software` folder.

![7-Zip MSI in software repository](../images/native-msi-gpo/03-software-share-with-7zip-msi.png)

## 4. Share the software folder

The `Software` directory was exposed as an SMB share so clients could access installers through a UNC path.

![Advanced sharing configuration](../images/native-msi-gpo/04-software-share-advanced-sharing.png)

## 5. Review share permissions

Read access was configured for domain users/principals that need to retrieve software packages.

![Domain Users share permissions](../images/native-msi-gpo/05-share-permissions-domain-users.png)

## 6. Add Domain Computers

Because the package is assigned under **Computer Configuration**, the domain computer security group was added to the deployment share permissions.

![Adding Domain Computers](../images/native-msi-gpo/06-add-domain-computers-to-share-permissions.png)

## 7. Confirm read permissions

The software share is intended for package retrieval, so domain computers receive read access rather than write access.

![Domain Computers read access](../images/native-msi-gpo/07-share-permissions-domain-computers-read.png)

## 8. Test the UNC path from the client

The Windows 10 client successfully browsed the server share and could see the 7-Zip MSI.

```text
\\WIN-OEFEH5TL5BD\Software
```

![Client access to software share](../images/native-msi-gpo/08-client-access-to-software-share.png)

## 9. Target the ICT OU

The Windows 10 computer account is located in the `ICT` OU, which provides the scope for the deployment GPO.

![ICT OU and client computer](../images/native-msi-gpo/09-ict-ou-client-computer.png)

## 10. Use the Software ICT GPO

The `Software ICT` GPO is linked to the `ICT` OU and used for the software installation policy.

![Software ICT GPO scope](../images/native-msi-gpo/10-software-ict-gpo-scope.png)

## 11. Assign the 7-Zip MSI

The MSI was added under:

```text
Computer Configuration
└── Policies
    └── Software Settings
        └── Software installation
```

The package source points to the server's UNC share and the deployment state is **Assigned**.

![7-Zip assigned in GPO](../images/native-msi-gpo/11-7zip-assigned-in-gpo.png)

## 12. Refresh Group Policy

On the Windows 10 client:

```cmd
gpupdate /force
```

Windows detected a computer policy that must be processed during startup and prompted for a restart.

![Group Policy restart required](../images/native-msi-gpo/12-gpupdate-restart-required.png)

## 13. Verify automatic installation

After reboot, 7-Zip appeared in the Start menu without the MSI being manually launched on the client.

![7-Zip installed](../images/native-msi-gpo/13-7zip-installed-start-menu.png)

## 14. Launch test

7-Zip File Manager opened successfully, confirming the application was deployed and usable.

![7-Zip launch successful](../images/native-msi-gpo/14-7zip-launch-success.png)

## Result

```text
7-Zip MSI -> Server Share -> Software ICT GPO -> Windows 10 Startup -> Installed
```

The native MSI deployment path was successful, providing a known-good baseline before moving to EXE repackaging.
