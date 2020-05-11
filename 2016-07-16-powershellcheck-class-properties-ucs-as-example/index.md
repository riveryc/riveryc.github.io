# PowerShell — Check Class Properties (UCS as Example)


While playing with UCS PowerShell command to adjust initial environment configuration... There are a lot of things that not following the "rule"

<!--more-->

Here is the JSON for an Object:

```JSON
{
  "BootMode":"legacy",
  "Descr":"",
  "EnforceVnicName":"yes",
  "Name":"BuildBootPolicy",
  "PolicyLevel":0,
  "PolicyOwner":"local",
  "PropAcl":0,
  "Purpose":"operational",
  "RebootOnUpdate":"no",
  "Sacl":null,
  "Ucs":"ucsmanager",
  "Dn":"org-root/boot-policy-BuildBootPolicy",
  "Rn":"boot-policy-BuildBootPolicy",
  "Status":null,
  "XtraProperty":{
  },
  "BootPolicyConfiguration":{
    "read-only-remote-cimc-vm":{
      "Access":"read-only-remote-cimc",
      "LunId":"0",
      "MappingName":"",
      "Order":1,
      "PropAcl":0,
      "Sacl":null,
      "Type":"virtual-media",
      "Ucs":"ucsmanager",
      "Dn":"org-root/boot-policy-BuildBootPolicy/read-only-remote-cimc-vm",
      "Rn":"read-only-remote-cimc-vm",
      "Status":null,
      "XtraProperty":{
      },
      "ClassId":"LsbootVirtualMedia"
    },
    "storage":{
      "Access":"read-write",
      "Order":2,
      "PropAcl":0,
      "Sacl":null,
      "Type":"storage",
      "Ucs":"ucsmanager",
      "Dn":"org-root/boot-policy-BuildBootPolicy/storage",
      "Rn":"storage",
      "Status":null,
      "XtraProperty":{
      },
      "ClassId":"LsbootStorage"
    }
  }
}
```

The structure of this object:

* BootPolicy
* BootPolicyConfiguration
* Boot device order

When I'm trying to add the first boot device order object, following command should be run:

```PowerShell
Add-UcsCentralManagedObject -Parent $UCSCentralBootPolicy -ClassId LsbootVirtualMedia -PropertyMap @{Access="read-only-remote-cimc";LunId=0;Order=1}
```

This command will pass the PropertyMap as hastable:

* Access
* LunId
* Order

But when I'm running the second boot device order object, if I still running the similar command based on the previous one:

```PowerShell
Add-UcsCentralManagedObject -Parent $UCSCentralBootPolicy -ClassId LsBootStorage -PropertyMap @{Access="read-write";Order=2}
```

It will trough the error message as

![screenshot](/static/img/post/20160716/powershell—check-class-properties-ucs-as-example-1.png)

Looks like the "Access" is a read-only property that cannot be set for this object, looks like they are not set in standard by Cisco PowerShell API Developer.

We need to find a way to detect when we need this property and when we don't.

By using decompiler ILSpy to check the DLL file (Cisco.UcsCentral.dll), find the class called LsbootVirtualMedia, we can see:

![screenshot](/static/img/post/20160716/powershell—check-class-properties-ucs-as-example-2.png)

But with LsBootStorage

![screenshot](/static/img/post/20160716/powershell—check-class-properties-ucs-as-example-3.png)

In PowerShell, we can use following method to check this property is writable or not:

![screenshot](/static/img/post/20160716/powershell—check-class-properties-ucs-as-example-4.png)

We need a more dynamic way as different object has different properties but in the same collection as a part of the Configuration.

So we need do this:

```PowerShell
$sc = "Cisco.UcsCentral.$ClassId" -as [type]
```

Then we can call following to find the property value we need:

```PowerShell
$sc::AccessMeta.Access
```

So good luck to my long journey for UCS Automation...
River
