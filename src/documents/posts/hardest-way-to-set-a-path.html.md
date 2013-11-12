```
type: post
layout: post
title: Hardest way to set a PATH
date: 2013-11-07 20:49
tags: ['powershell']
```

In a [previous post](/posts/setting-up-multiple-rubies-on-windows/) I hacked together a cumbersome Powershell statement when I tried to set a path from the shell in a way that anyone could use. And it also had to be persistent.

Using <code>$env:Path</code> only sets the path for that session so it wasn't an option since it would leave people hanging when they restarted the shell.<!-- read more -->

It kinda stumped me for a while because I found it disturbingly ugly but didn't want to go off on a tangent to set up a function. So here is that tangent!
    
Open up powershell and set your execution policy unless you've already done so.

``` no-highlight
PS C:\> Set-ExecutionPolicy Unrestricted -Scope CurrentUser
```

then enter

``` no-highlight
PS C:> $profile
```

This will give you the path to your startup script, the file will not exist unless you've set it up before. This script runs every time you open a PowerShell.

Go create that file and open it in your prefered text editor. Then paste the following two functions into it.

```
function Add-Path { 
  param ( 
    [Parameter(Mandatory=$true, ValueFromPipeline=$true)]
    $Path 
  )

  if (-not (Test-Path -LiteralPath $Path -IsValid)) {
    Write-Output "'$Path' is not a valid path."
    return 
  }

  $EnvironmentScope = "User"
  $PATHVariable = [environment]::GetEnvironmentVariable("PATH", $EnvironmentScope)
  $ExistingPaths = $PATHVariable.Replace(";;", ";").Split(";")
  if ($ExistingPaths.Contains($Path)) {
    Write-Output "'$Path' is already in your environment PATH." 
  } else {
    $UpdatedPATHVariable = [system.String]::Join(";", $ExistingPaths + $Path).Replace(";;", ";") 
    [environment]::SetEnvironmentVariable("PATH", $UpdatedPATHVariable, $EnvironmentScope) 
  }
}
```

```
function Remove-Path {
  param ( 
    [Parameter(Mandatory=$True, ValueFromPipeline=$True)]
    $Path
  )

  $EnvironmentScope = "User"
  $RemainingPaths = New-Object System.Collections.Generic.List[String]
  foreach ($existingPath in [environment]::GetEnvironmentVariable("PATH", $EnvironmentScope).Replace(";;", ";").Split(";")) {
    if (-not $existingPath.Equals($Path)) {
      $RemainingPaths.Add($existingPath)
    }
  }

  [environment]::SetEnvironmentVariable("PATH", [system.String]::Join(";", $RemainingPaths), $EnvironmentScope)
}
```

Now adding and removing a path to your PATH variable is as easy as

``` no-highlight
PS C:> Remove-Path C:\MyOldPath
PS C:> Add-Path C:\MyNewPath
```
