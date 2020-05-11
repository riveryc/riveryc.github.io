# Ternary Operator in PowerShell


I’m very noob .Net C# developer, but I remember this:

```C#
<compare> ? <value of true> : <value of false>
(a == ‘a’ ) ? 1 : 2
```

<!--more-->

I want the same thing in PowerShell, here we go:

```PowerShell
. ({'condition is false'},{'condition is true'})[$condition]
```

Sample:

```PowerShell
@("false","true")["a" -eq "b"]
```

Which I came up with a more readable and something easy to memory:

```PowerShell
@{$true=1;$false=2}[$a -eq 'a']
```

Happy scripting….
