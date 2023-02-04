---
title: PowerCLI that can save you a lot of time
description: List of PowerCLI one-liners that could be helpful to speedup work.
tags: [vmware, powercli, cmd, scripting, automation]
date: 2021-03-19 20:00:00
---

# PowerCLI one-liners
Here are some on-liners which back in the days when i was working with VMware i always had in my pocket as they help to save a lot of time by finding something or automate some process.


Get VM from host cluster on specified datastore
```
Get-Cluster "CLUSTER_NAME" | Get-VM |?{($_.extensiondata.config.datastoreurl|?{$_.name -eq "ESXI_NAME"})}
```

List snapshots older than 3 days
```
Get-VM | Get=Snapshot | Where {$_.Created -Lt (Get-Date).AddDays(-3)} | Select-Object VM, Name, Created
```


Delete snapshots older than 3 days
```
Get-VM | Get=Snapshot | Where {$_.Created -Lt (Get-Date).AddDays(-3)} | Remove-Snapshot -Confirm:$false
```


Free space on datastores connected to the host
```
Get-Datastore -VMHost ESXI_NAME| select-object name, CapacityGB, @{Label="FreespaceGB";E={"{0:n2}" -f($_.FreespaceGB)}} | sort name
or
get-datastore | select-object name, CapacityGB, @{Label="FreespaceGB";E={"{0:n2}" -f($_.FreespaceGB)}}

```


Datastore name & naa
```
$esxName = "ESXI_NAME"
Connect-VIServer vcenter_server_name
Get-VMHost -Name $esxName | Get-Datastore |
Where-Object {$_.ExtensionData.Info.GetType().Name -eq "VmfsDatastoreInfo"} |
ForEach-Object {
if ($_)
{
$Datastore = $_
$Datastore.ExtensionData.Info.Vmfs.Extent |
Select-Object -Property @{Name="Name";Expression={$Datastore.Name}},
DiskName
}
}
```

Datastore NAA
```
get-datastore | ? { $_.Name -like "DATASTORE_NAME" } | Select Name, @{N="Naa";E={$_.extensiondata.info.vmfs.extent.diskname}} | Sort Name
```


ESXi WWN
```
Get-Cluster "CLUSTER_NAME" | Get-VMhost | Get-VMHostHBA -Type FibreChannel | where {$_.Status -eq "online"} | Select @{N="Cluster";E={$cluster}},VMHost,Device,@{N="WWN";E={"{0:X}"-f$_.PortWorldWideName}} | Sort VMhost,Device
```


Host utilization
```
Get-VMHost | Sort-Object -Property Name |
Format-Table Name,State,CpuTotalMhz,CpuUsageMhz, MemoryTotalMB, MemoryUsageMB,
  @{N="Status";E={$_.ExtensionData.Summary.OverallStatus}},
  @{N="CPU%";E={[Math]::Round(100*$_.ExtensionData.Summary.QuickStats.OverallCpuUsage/($_.ExtensionData.Summary.Hardware.CpuMhz*$_.ExtensionData.Summary.Hardware.NumCpuCores),0)}},
  @{N="Mem%";E={[Math]::Round(100*$_.ExtensionData.Summary.QuickStats.OverallMemoryUsage*1MB/$_.ExtensionData.Summary.Hardware.MemorySize,0)}} | Export-csv c:\temp\myreportutilization.csv
```

Start VM
```
$vms = get-vm ; foreach ($vm in $vms){Start-VM -VM $vm.name}
```


Shutdown-guest-OS
```
$vms = get-vm ; foreach ($vm in $vms){Shutdown-VMGuest -VM $vm.name -Confirm:$false}
```


Stop VM - shutdown VM /!\ No guest OS shutdown /!\
```
$vms = get-vm ; foreach ($vm in $vms){Stop-VM -VM $vm.name -Confirm:$false}
```


Shutdown-guest-OS per datastore
```
$vms = get-vm -datastore [datastore] ; foreach ($vm in $vms){Shutdown-VMGuest -VM $vm.name -Confirm:$false}
```


Remove VM (delete Permanently)
```
$vms = get-vm ; foreach ($vm in $vms){Remove-VM -VM $vm.name -DeletePermanently -Confirm:$false}
```


Unmount virtual CD-ROM
```
Get-VM <filters> | Get-CDDrive | Set-CDDrive -NoMedia -Confirm:$false
```


Rescan SAN (all)
```
Get-VMHost | Get-VMHostStorage -RescanAllHba
Get-VMHost | Get-VMHostStorage -RescanVMFS
```

Rescan HBA and NFS in cluster
```
Get-Cluster -Name “CLUSTER_NAME” | Get-VMHost | Get-VMHostStorage -RescanAllHba
```

Check SAN Policy
```
$hs = get-vmhost ; foreach ($h in $hs){get-scsilun -vmhost $h -luntype disk}
```


Set SAN policy to Round Robin
```
$hs = get-vmhost ; foreach ($h in $hs){get-scsilun -vmhost $h -luntype disk | set-scsilun -multipathpolicy "RoundRobin"}

or only for LUN path with fixed policy

$hs = get-vmhost ; foreach ($h in $hs){get-scsilun -vmhost $h -luntype disk | where {$_.MultipathPolicy -eq "Fixed"} | set-scsilun -multipathpolicy "RoundRobin"}
```

VMotion Bulk !
```
Get-VM -Location (Get-VMHost ‘<filters>’) | Move-VM -Destination (GetVM-Host ‘<filters>’)
```


Vmotion Bulk ! (sequential)
```
$vms = get-vm <filters> -Location <filters> ; foreach($vm in $vms){ $task = move-vm $vm -Destination <filters> -RunAsync ; Wait-Task -Task $task }
```


VMotion Bulk ! With selected vm
```
$vms = get-vm -Location <filters> | sort MemoryGB | Select-Object -last 30 ; foreach($vm in $vms){ $task = move-vm $vm -Destination <filters> -RunAsync ; Wait-Task -Task $task }
```


Move to folder
```
get-vm <filters> | move-vm -destination <filters>
```


Get VM Size (GB)
```
get-vm | get-view | Select @{N="Name";E={$_.name}}, @{N="Size(GB)";E={[math]::Round((($_.Storage.PerDatastoreUsage.Committed)+($_.Storage.PerDatastoreUsage.Uncommitted))/ 1GB)}}, @{N="Used(GB)";E={[math]::Round(($_.Storage.PerDatastoreUsage.Committed)/ 1GB)}}
```


Get VM need disk consolidate
```
Get-VM | Where-Object {$_.Extensiondata.Runtime.ConsolidationNeeded}
```


Consolidate snapshot
```
Get-VM | Where-Object {$_.Extensiondata.Runtime.ConsolidationNeeded} | ForEach-Object {$_.ExtensionData.ConsolidateVMDisks()}
```

Search VM on Datastore
```
	dir -Recurse -Path vmstores:\ -Include *.vmx | select Name,DatastoreFullPath
```


Parse All pNics with model, drivers, drivers, version
```
$allvmnics = @() ; $vmnics = @() ; $vmhosts = get-vmhost | Where-Object {$_.ConnectionState -eq "Connected"} | sort name ; foreach ($vmhost in $vmhosts) { $vmnics += get-vmhostnetworkadapter -Physical -vmhost $vmhost | Select @{N="HostName"; E={$vmhost}}, DeviceName } ; foreach ($vmnic in $vmnics) { $esxcli = get-esxcli -vmhost $vmnic.Hostname ; $allvmnics += $vmnic | select @{N="Hostname" ; E={$vmnic.HostName}}, @{N="DeviceName" ; E={$vmnic.DeviceName}}, @{N="NicModel" ; E={$nicmodel = $esxcli.network.nic.list() | where-object {$vmnic.DeviceName -eq $_.name}; $nicmodel.Description}} , @{N="Drivers" ; E={$esxcli.network.nic.get($vmnic.DeviceName).DriverInfo.Driver}}, @{N="DriverVersion";E={$esxcli.network.nic.get($vmnic.DeviceName).DriverInfo.Version}}} ; $allvmnics | Sort-Object Hostname, DeviceName | Format-Table -auto
```


Parse network info (CDP)
```
$cdpinfo = @() ; Get-VMHost | Where-Object {$_.ConnectionState -eq "Connected"} | sort Name | %{Get-View $_.ID} | %{$esxname = $_.Name; Get-View $_.ConfigManager.NetworkSystem} | %{ foreach($physnic in $_.NetworkInfo.Pnic){ $pnicInfo = $_.QueryNetworkHint($physnic.Device); foreach($hint in $pnicInfo){ if( $hint.ConnectedSwitchPort ) { $cdpinfo = $hint.ConnectedSwitchPort | select SystemName, PortId, Location } else { $cdpinfo = "No CDP information available." } ; write-host $esxname $physnic.Device $cdpinfo.SystemName $cdpinfo.PortId $cdpinfo.Location } } }
```


Get vcenter, cluster, esx
```
get-vmhost | Select @{N="vCenter";E={$_.ExtensionData.CLient.ServiceUrl.Split('/')[2]}}, @{N="Cluster";E={get-cluster -vmhost $_.Name}}, Name
```


Get dVswitch info

	get-vmhost | sort Name | %{ $esxname = $_.Name ; get-vdswitch -vmhost $esxname} | %{ $dvswitch = $_.Name ; Get-VDport -VDSwitch $dvswitch -uplink } | Get-VDUplinkLacpPolicy | Select @{N="Hostname";E={$esxname}}, @{N="DVswitch";E={$dvswitch}}, @{N="LACPEnabled";E={$_.Enabled}}, @{N="LACPMode";E={$_.Mode}} | group-object DVswitch


Find the biggest VM on the fullest DS
```
get-vm -datastore (get-datastore <filter>) | get-view | Select @{N="Name";E={$_.name}}, @{N="Size(GB)";E={[math]::Round((($_.Storage.PerDatastoreUsage.Committed)+($_.Storage.PerDatastoreUsage.Uncommitted))/ 1GB)}}, @{N="Used(GB)";E={[math]::Round(($_.Storage.PerDatastoreUsage.Committed)/ 1GB)}} | Sort "Used(GB)" | select-object -last 1
```


Get NTP configuration by host (updated)

	Get-VMHost | Where-Object {$_.ConnectionState -ne "Disconnected"} | Sort Name | Select-Object @{N="vCenter";E={$_.ExtensionData.CLient.ServiceUrl.Split('/')[2]}}, Name, @{Name="NTPServer";Expression={$_ | Get-VMHostNtpServer}}, @{Name="NTPRunning";Expression={($_ | Get-VMHostService | Where-Object {$_.key -eq "ntpd"}).Running}} | ft -auto


Get DvPortGroup per VM for a datastore

	$vms = get-vm -datastore DATASTORE_NAME ; foreach ($vm in $vms){$DvPortGroup = Get-VirtualPortGroup -vm $vm.Name ; $vm | Select @{N="Name";E={$_.name}}, @{N="DvPortGroup";E={$DvPortGroup.Name}}} | ft -autosize


Add NFS datastore on All Hosts on a Cluster
```
get-cluster <filter> | get-vmhost | New-Datastore -Nfs -Name <name> -Path <path> -NfsHost <nfsname|@ip>
```


Export all portgroup
```
Get-VDPortgroup | Select Name, VirtualSwitch, Datacenter, VlanConfiguration | Export-Csv "file.csv"
```


Get-VM PowerOff with Notes
```
Get-vm | where { $_.PowerState -eq "PoweredOff"} | Select @{N="Name";E={$_.Name}},@{N="PowerState";E={$_.PowerState}},@{N="Note";E={Get-VM $_ | Select-Object -ExpandProperty Notes}}
```
	
	
PowerOff VM from list
```
foreach($vmlist in (Get-Content -Path C:\TEMP\vmlist.txt)){ $vm = Get-VM -Name $vmlist Shutdown-VMGuest -VM $vm -Confirm:$false}
```


Get VM with technical stuff
```
Get-vm | Select @{N="Name";E={$_.Name}},@{N="PowerState";E={$_.PowerState}}, @{N="vCenter";E={(get-vmhost -VM $_.Name).ExtensionData.CLient.ServiceUrl.Split('/')[2]}}, @{N="Cluster";E={(Get-Cluster -vmhost (get-vmhost -VM $_.Name).Name).Name}}, @{N="VMhost";E={(get-vmhost -VM $_.Name).Name}} | ft -autosize
```


Get-datastore Naa
```
get-datastore | ? { $_.Name -like <filter> } | Select Name, @{N="Naa";E={$_.extensiondata.info.vmfs.extent.diskname}}
```


Get WWN with HBA Status for all Hosts
```
Get-VMHost | Get-VMHostHBA -Type Fibrechannel | Select @{N="vCenter";E={(get-vmhost $_.VMHost).ExtensionData.CLient.ServiceUrl.Split('/')[2]}}, @{N="Cluster";E={(get-vmhost $_.VMHost).Parent}}, VMHost, Device, Status, @{N="WWN";E={"{0:X}" -f $_.PortWorldWideName}} | Sort vCenter, Cluster, VMHost, Device | ft -auto
```


Get-VM by selected guest OS

	Get-VM | Where {$_.PowerState -eq "PoweredOn" -and $_.Guest.OSFullName -like "<filter>"} | select @{N="Name";E={$_.Name}}, @{N="Guest-OS";E={$_.Guest.OSFullName}}


Get-VM by name / OS

	get-vm | Select @{N="Name";E={$_.name}}, @{N="GuestOS";E={$_.Guest.OSFullName}}


Get VM Stats (24h)

	get-vm | Where {$_.PowerState -eq "PoweredOn"} | Sort Name | Select Name, Host, NumCpu, MemoryMB, @{N="Cpu.UsageMhz.Average";E={[Math]::Round((($_ |Get-Stat -Stat cpu.usagemhz.average -Start (Get-Date).AddHours(-24)-IntervalMins 5 -MaxSamples (12) |Measure-Object Value -Average).Average),2)}}, @{N="Mem.Usage.Average";E={[Math]::Round((($_ |Get-Stat -Stat mem.usage.average -Start (Get-Date).AddHours(-24)-IntervalMins 5 -MaxSamples (12) |Measure-Object Value -Average).Average),2)}} | Format-Table -auto


Get VM Stats (30 Days)

	Get-VM | Where {$_.PowerState -eq "PoweredOn"} | Select Name, Host, NumCpu, MemoryMB, @{N="CPU(100)" ; E={[Math]::Round((($_ | Get-Stat -Stat cpu.usage.average -Start (Get-Date).AddHours(-720) -IntervalMins 5 -MaxSamples (8640) | Measure-Object Value -Average).Average),2)}}, @{N="Memory(100)" ; E={[Math]::Round((($_ | Get-Stat -Stat mem.usage.average -Start (Get-Date).AddHours(-720) -IntervalMins 5 -MaxSamples (8640) | Measure-Object Value -Average).Average),2)}} , @{N="Network(KBps)" ; E={[Math]::Round((($_ | Get-Stat -Stat net.usage.average -Start (Get-Date).AddHours(-720) -IntervalMins 5 -MaxSamples (8640) | Measure-Object Value -Average).Average),2)}} | Format-Table -auto


Star SSH daemon on all host in a cluster
```
Get-Cluster | Get-VMHost | ForEach {Start-VMHostService -HostService ($_ | Get-VMHostService | Where {$_.Key -eq “TSM-SSH”})}
```


Storage information per VM

	get-cluster | get-vmhost | get-vm | Get-HardDisk | Select @{N="Host";E={$(get-vm $_.Parent).host}}, Parent, CapacityGB, @{N="RealUsageGB";E={[math]::Round(((($_.Parent | get-view).Storage.PerDatastoreUsage.Committed)+(($vm | get-view).Storage.PerDatastoreUsage.Uncommitted))/ 1GB)}}, StorageFormat, Filename


Get Uptime for each vmhost
```
Get-VMHost | Get-View | select Name, @{N="Uptime"; E={(Get-Date) - $_.Summary.Runtime.BootTime}} | Sort Name
```


Search iso image on all datastores
```
dir -Recurse -Path vmstores:\ -Include *.iso | select Name,DatastoreFullPath
```


Manage CD image
```
New-CDDrive -VM VM -ISOPath "DatastoreFullPath"

Get-VM (filter vm) | Get-CDDrive | Where { $_.IsoPath } | Select Parent, IsoPath, ConnectionState

get-vm (filter vm) | Get-CDDrive | Where { $_.IsoPath } | Set-CDDrive -connected:$true -Confirm:$false

New-CDDrive -VM VM -ISOPath "[sof-20666-esx:storage1] ISO\testISO.iso"
```

Get VM @MAC
```
get-vm | Select @{N="vCenter";E={(get-vmhost -VM $_.Name).ExtensionData.CLient.ServiceUrl.Split('/')[2]}}, @{N="Cluster";E={(Get-Cluster -vmhost $_.VMhost).Name}}, VMhost, Name, @{N="PowerState";E={$_.PowerState}}, @{N="MAC";E={($_ | Get-NetworkAdapter).MacAddress}}, @{N="MACType";E={($_ | Get-NetworkAdapter).ExtensionData.AddressType}}
```


Get VM with Ping status and host
```
Get-VM | Select Name, PowerState, @{N="Ping";E={if($_.PowerState -eq "PoweredOn" -and (Test-Connection -ComputerName $_.Name)){"Online"}else{"Offline"}}}, VMHost | ft -auto
```


Check hardware acceleration
```
get-vmhost | where-object {$_.ConnectionState -eq "Connected"} | Sort Name | Select Name, @{N="Move";E={ ($_ | Get-AdvancedSetting -name DataMover.HardwareAcceleratedMove).Value }}, @{N="Init";E={ ($_ | Get-AdvancedSetting -name DataMover.HardwareAcceleratedInit).Value }}, @{N="VMFS3Lock";E={ ($_ | Get-AdvancedSetting -name VMFS3.HardwareAcceleratedLocking).Value }}, @{N="VMFS3Delete";E={ ($_ | Get-AdvancedSetting -name VMFS3.EnableBlockDelete).Value }}, @{N="TransferSize";E={ ($_ | Get-AdvancedSetting -name DataMover.MaxHWTransferSize).Value }} | ft -auto
```


Get-VM with multi lines Notes
```
Get-VM | Select @{N="Name";E={$_.Name}}, @{N="Notes";E={$_.Notes -replace("`r`n", " ")}}

or

Get-VM | Select @{N="Name";E={$_.Name}}, @{N="Notes";E={$_.Notes -replace("`n", " ")}}
```

Cluster + host + datastore Naa + MultipathPolicy + DS size
```
Get-Cluster | sort Name | get-vmhost | where {$_.ConnectionState -eq "Connected"} | sort name | get-scsilun -LunType disk | Select @{N="Cluster";E={(get-vmhost $_.VMHost).Parent}}, VMHost, CanonicalName, MultipathPolicy, CapacityGB | ft -auto
```

