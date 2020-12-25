# Server 2008 R2 EpadLink versioning bug over RDS

---

> Universal Installer | ePadLink - Latest Version v12.4 (*ATTOW*)
> - [Universal Installer - Remote Usage Guide](https://epadlink.com/guides/UniversalInstaller-RemoteUsage.pdf)
> - [RemoteFX Support for ePadLink Signature Pads](http://www.epadsupport.com/downloads/ePadLinkRemoteFX.pdf)

## Summary

December 2020 - issue discovered between latest ePad II drivers installed on various releases of Windows 10 and the USB-redirection services running on a Server 2008 RDSH virtual machine. Devices continue to function normally on localhost OS, but are not detected by the remote server.

As all versions of Server 2008 have since been deprecated, vendor is not likely to issue a patch for bug's underlying cause.

## Workaround

It has been discovered that by enabling by enabling group policy on the Windows 10 client to support RemoteFX USB Redirection Services and installing the latest ePad Universal Installer on both client and server, device will once again be able to communicate over RDP. 

This remains true whether or not the RDSH vm has been configured to meet the [RemoteFX vGPU Requirements](https://en.wikipedia.org/wiki/RemoteFX#Requirements).

Follow the steps listed in [second document](https://en.wikipedia.org/wiki/RemoteFX#Requirements) embedded in this page