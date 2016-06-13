# Features
By default, server is missing some features.
Full list of features is available via `Get-WindowsFeature` cmdlet:
```
Get-WindowsFeature|Out-GridView
```

I need HyperV, Desktop Experience and WiFi:
```
Add-WindowsFeature Hyper-V,Wireless-Networking,Desktop-Experience –restart
```

