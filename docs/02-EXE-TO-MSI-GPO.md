# EXE to MSI Repackaging and GPO Deployment

This section documents the second stage of the lab: converting the **VLC Media Player EXE installer** into an MSI with Advanced Installer Repackager and then automatically deploying the generated MSI through Group Policy.

[Back to main README](../README.md)

## 1. Download Advanced Installer

Advanced Installer 23.9 was selected as the packaging tool for the lab.

![Advanced Installer download page](../images/exe-to-msi-gpo/01-advanced-installer-download-page.png)

## 2. Download the installer

The Advanced Installer setup package was downloaded to the Windows 10 VM.

![Advanced Installer download progress](../images/exe-to-msi-gpo/02-advanced-installer-download-progress.png)

## 3. Install Advanced Installer

Advanced Installer was installed on the Windows 10 packaging/client VM rather than on the domain controller.

![Installing Advanced Installer](../images/exe-to-msi-gpo/03-advanced-installer-installation.png)

## 4. Open Advanced Installer

The application opens to its project selection screen.

![Advanced Installer home](../images/exe-to-msi-gpo/04-advanced-installer-home.png)

## 5. Select Repackage Installation

The **Repackage Installation** project type was selected. This workflow captures changes made by an existing installer and converts them into an MSI project.

![Repackage Installation project](../images/exe-to-msi-gpo/05-repackage-installation-project.png)

## 6. Download the VLC EXE

VLC Media Player was chosen as the EXE packaging example.

![VLC download page](../images/exe-to-msi-gpo/06-vlc-download-page.png)

## 7. Select the VLC installer

The VLC 3.0.23 Win32 EXE was selected as the application to repackage.

![Select VLC EXE](../images/exe-to-msi-gpo/07-select-vlc-exe.png)

## 8. Configure the capture

Advanced Repackager shows the VLC setup path and prepares the local capture session.

![Repackager package configuration](../images/exe-to-msi-gpo/08-repackager-package-configuration.png)

## 9. Save the Repackager project

The capture project was saved before starting the system snapshot process.

![Save Repackager project](../images/exe-to-msi-gpo/09-save-repackager-project.png)

## 10. Capture the initial system state

Advanced Repackager scans the machine before VLC is installed. This creates the baseline used for the before/after comparison.

![Initial system snapshot](../images/exe-to-msi-gpo/10-initial-system-snapshot.png)

## 11. Start the VLC installer

The VLC installation begins while the Repackager session remains active.

![VLC installer language](../images/exe-to-msi-gpo/11-vlc-installer-language.png)

## 12. Install VLC normally

The installer was allowed to place VLC in its normal Program Files location so those installation changes could be captured.

![VLC install location](../images/exe-to-msi-gpo/12-vlc-install-location.png)

## 13. Begin the post-install scan

After the VLC installer finished, the Repackager prompted to continue with the next stage.

![Ready for post-install scan](../images/exe-to-msi-gpo/13-repackager-ready-for-after-scan.png)

## 14. Capture completes successfully

The second system scan completed and the Repackager generated the captured application data.

![Repackager capture complete](../images/exe-to-msi-gpo/14-repackager-capture-complete.png)

## 15. Import the Repackager results

The captured files, folders, and configuration changes were imported into an Advanced Installer MSI project.

![Import Repackager results](../images/exe-to-msi-gpo/15-import-repackager-results.png)

## 16. Configure product details

The imported project was identified as VLC Media Player and the publisher was set to VideoLAN.

![VLC product details](../images/exe-to-msi-gpo/16-vlc-product-details.png)

## 17. Save the Advanced Installer project

The MSI project was saved as an `.aip` project before the build stage.

![Save Advanced Installer project](../images/exe-to-msi-gpo/17-save-advanced-installer-project.png)

## 18. Select a Single MSI build

The package type was configured as **Single MSI (resources inside)** so the result would be one deployable MSI file.

![Single MSI build configuration](../images/exe-to-msi-gpo/18-single-msi-build-configuration.png)

## 19. Build the package

Advanced Installer generated the MSI package from the captured application data.

![Building MSI package](../images/exe-to-msi-gpo/19-building-msi-package.png)

## 20. Confirm build success

The build finished successfully.

![MSI build successful](../images/exe-to-msi-gpo/20-msi-build-success.png)

## 21. Verify the generated MSI

The output folder contains the generated `VLC Media Player` Windows Installer package.

![Generated VLC MSI](../images/exe-to-msi-gpo/21-generated-vlc-msi.png)

## 22. Move the package toward the software repository

The new MSI was moved from the packaging output location toward the deployment share workflow.

![Copy MSI to software share](../images/exe-to-msi-gpo/22-copy-msi-to-software-share.png)

The client share itself is intentionally read-oriented; administrative package management is performed from the server side.

## 23. Confirm the package on the server

The server's software repository now contains both the native 7-Zip MSI and the repackaged VLC MSI.

![Server share with VLC MSI](../images/exe-to-msi-gpo/23-server-software-share-with-vlc-msi.png)

## 24. Add VLC to Software Installation

The new VLC MSI was selected from the network software share inside the `Software ICT` Group Policy Object.

![Select VLC MSI in GPO](../images/exe-to-msi-gpo/24-select-vlc-msi-in-gpo.png)

## 25. Confirm both assigned applications

The GPO now contains two assigned packages:

- 7-Zip 26.01 x64
- VLC Media Player

![VLC assigned in GPO](../images/exe-to-msi-gpo/25-vlc-assigned-in-gpo.png)

## 26. Force Group Policy and restart

The Windows 10 client refreshed Group Policy:

```cmd
gpupdate /force
```

The software installation policy required startup processing, so the client was restarted.

![Group Policy refresh for VLC deployment](../images/exe-to-msi-gpo/26-gpupdate-restart-for-vlc-deployment.png)

## 27. Verify VLC installation

After reboot, VLC appeared in the Windows Start menu without manually running the generated MSI on the client.

![VLC installed in Start menu](../images/exe-to-msi-gpo/27-vlc-installed-start-menu.png)

## 28. Launch test

VLC Media Player launched successfully, confirming that the EXE-to-MSI package and GPO deployment both worked end to end.

![VLC launch successful](../images/exe-to-msi-gpo/28-vlc-launch-success.png)

## Result

```text
VLC EXE
   -> Advanced Repackager before/after capture
   -> Generated MSI
   -> Windows Server software share
   -> Software ICT GPO (Assigned)
   -> Windows 10 startup processing
   -> VLC installed automatically
```

This completed the full application-packaging and centralized-deployment workflow.
