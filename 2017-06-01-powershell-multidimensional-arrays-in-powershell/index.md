# PowerShell - Multidimensional Arrays in PowerShell


You may have some arrays like this:

```PowerShell
$year1 = @()
$year1 += "Cindy"
$year1 += "Candy"
$year1 += "Mandy"
$year2 = @()
$year2 += "Chris"
$year2 += "Green"
$year2 += "Richard"
```
<!--more-->

Then you may want to combine them together:

```PowerShell
$School = @()
$School += $year1
$School += $year2
```

But unfortunately it’s not simple like this…

You will get:

```PowerShell
PS C:\River> $School.Count
6
```

Suppose to be 2, right? Why it’s 6… Let’s identify…

```PowerShell
PS C:\River> foreach ($item in $School){ Write-Host "Item index: $($School.IndexOf($item)), Item value: $($item)" }
Item index: 0, Item value: Cindy
Item index: 1, Item value: Candy
Item index: 2, Item value: Mandy
Item index: 3, Item value: Chris
Item index: 4, Item value: Green
Item index: 5, Item value: Richard
```

But what I want it to be is `$School` contains `$year1` and `$year2`, and `$year1` contains its own people, the same as `$year2`.

Here is the solution…

```PowerShell
$School = @()
$School += ,$year1
$School += ,$year2
```

Let’s double check the new array:

```PowerShell
PS C:\River> foreach ($item in $School){ Write-Host "Item index: $($School.IndexOf($item)), Item value: $($item)" }
Item index: 0, Item value: Cindy Candy Mandy
Item index: 1, Item value: Chris Green Richard
```

Whooya~~ But what if we add a new year?

```PowerShell
$year3 = @()
$year3 += “John”
$year3 += “Alex”
$year3 += “Pete”
$School += ,$year3
foreach ($item in $School){ Write-Host “Item index: $($School.IndexOf($item)), Item value: $($item)” }
Item index: 0, Item value: Cindy Candy Mandy
Item index: 1, Item value: Chris Green Richard
Item index: 2, Item value: John Alex Pete
```

What if we add a new year with more or less members?

```PowerShell
$year4 = @()
$year4 += “Micheal”
$year4 += “Barry”
$year4 += “Charlie”
$year4 += “Doug”
$School += ,$year4
foreach ($item in $School){ Write-Host “Item index: $($School.IndexOf($item)), Item value: $($item)” }
Item index: 0, Item value: Cindy Candy Mandy
Item index: 1, Item value: Chris Green Richard
Item index: 2, Item value: John Alex Pete
Item index: 3, Item value: Micheal Barry Charlie Doug
```

What if we remove a year?

```PowerShell
$School = $School | ? {$_[0] -ne “Cindy”}
```

The reason why we do this is due to the limitation in PowerShell Array operation. It would be much easier if we use ArrayList. :)

Enjoy your coding...
