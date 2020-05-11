# Send Email via PowerShell


I need to send a large number (more than 100) emails to my wife for her needs.

She has a folder that contains tones pdf files, needs to be send to an email address one file per email.

<!--more-->

So I created this script to send the mail…

For sure you need to modify this if you are going to send 100 times by using foreach loop…

```PowerShell
$smtpserver = "smtp.telstra.com"
$file = Get-ChildItem -Path "C:\temp\file.pdf"
$sender = Read-Host -Prompt 'Please enter the email you want to send from'
$recipient = Read-Host -Prompt 'Please enter the email you want to send to'
$msg = New-Object System.Net.Mail.MailMessage
if ($file -ne $null)
{
    $att = New-Object System.Net.Mail.Attachment($file.FullName)
    $msg.Attachments.Add($att)
}
$smtp = New-Object System.Net.Mail.SmtpClient($smtpserver)
$msg.From = $sender
$msg.To.Add($recipient)
$msg.Subject = "$file.Name"
$msg.Body = "It's size is $($file.Length) bytes"
$smtp.Send($msg)
```

Just a hint, have fun~

