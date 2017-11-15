# Setting up Chocolatey for Organizational Use

While Chocolatey open source (FOSS) is a great solution for personal use due the ability to download and install packages from the Chocolatey community repository, organizations looking to leverage Chocolatey internally will likely want to take a different approach to using Chocolatey. Most of the reasons for this is gaining more control over how clients interact with packages, which will increase the security surrounding managing software. Organizations may also look to create their own Chocolatey packages, especially for software that is not found in the public repository, which therefore cannot be internalized to an internal feed. In terms of managing packages on organizational clients, organizations will want to use Chocolatey along with remote tools, such as SCCM, PowerShell, Puppet or Chef.

## Setting up Chocolatey clients

The minimal requirements for installing Chocolatey on a client computer is as follows:
* Windows 7+/Windows Server 2003+
* PowerShell v2+
* .Net Framework 4.0+

In this example, I want to install Chocolatey with PowerShell. I can simple run:

Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))

If you are deploying Chocolatey in bulk it is recommended ?...

### Additional configuration for clients

One change to your Chocolatey client configuration you will likely want to make is adding a source to your internal feed in additional to removing the public Chocolatey feed as your default source. To add a source to your client you can use the choco source command:

* choco source add --name='MyFeed' --source='https://yourfeed:443/chocolatey' --priority=0<br>
* choco source remove --name=chocolatey

Here I added my hosted feed named 'MyFeed' as a source and removed the community feed. This means that by default clients will not attempt to download and install packages from the community feed unless specified.

Recommended additional configs for org use?

While Chocolatey FOSS is acceptable for organizational use, you can also purchase a business license (C4B) in order to leverage the many features including but not limited to malware protection, CDN cache, and background mode.

## Using Chocolatey offline

One of the first tasks that an organization should do is create their own package repository. This quickly allows greater security and vetting of packages that Chocolatey clients will be installing. Although the Chocolatey public repository is moderated, it is not meant to be a completely trusted source of packages, thus organizations should look to host their own feed internally and remove the dependency on the public feed so that clients do not reach out to the internet for packages. Another aspect of the public feed that is important pertains to distribution rights of the packages. Most packages in the public feed actually download installers directly from the official distribution points, which is necessary for legal purposes, thus binaries are not included in the actual Chocolatey package you download. 

There are several solutions for hosting your own feed for Chocolatey packages, documented [here](https://chocolatey.org/docs/how-to-host-feed). The two most simple would be either to have packages in a CIFS share or deploy the Chocolatey Simple Server. This will allow organizations to easily manage their own packages without any dependency on the public feed/internet access.

## Creating your own packages 

The ability to create Chocolatey packages that only your organization uses, is one of the great features of Chocolatey. Depending on the complexity of the package, some can even be created to completion with one Chocolatey command; choco new. For packages that are more complex, users can leverage features such as providing other packages as dependencies, custom arguments for installation and PowerShell extensions.

## Internalizing packages from the community feed

Many organizations choose to reconfigure public feed packages for their own use. This can be done manually with Chocolatey FOSS by downloading the package, unzipping its contents, downloading additional necessary resources (such as installers from their distribution points), editing the Chocolatey install script, packing it back up into a NuGet packages and finally pushing to your internal repository. Although, you can automate this with the Chocolatey for Business license:

Here, I internalize the Google Chrome package from the community repository:
'choco download googlechrome --internalize --source='chocolatey''

Now I can push this package to my internal feed with the choco push command:
'choco push googlechrome --source=http://myfeed/ --api-key='12345678''

## Managing Chocolatey with remote tools

Once the Chocolatey client is deployed, it will likely be the goal for organizations to manage their Chocolatey clients via remote tools included but not limited to SCCM, Puppet, Chef or DSC. Regardless of what tool your organization uses, most popular tools are supported by Chocolatey as illustrated [here](https://chocolatey.org/docs/features-infrastructure-automation). 

