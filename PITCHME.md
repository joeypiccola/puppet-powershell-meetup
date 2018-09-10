## @color[#2e75e8](PowerShell) + @color[#FE9F17](Puppet)
### Tasks, Chocolatey, DSC

---

## Agenda

- Puppet Tasks with PowerShell
- Upgrade PowerShell with Tasks & Chocolatey
- Desired State Configuration (DSC

---

#### Puppet Tasks

@ol[](false)
- Tasks are single actions that you run on target machines in your infrastructure.
- Best for changes that aren’t about enforcing the state of machines.
- You can write tasks in any programming language that can run on the target nodes.
- Tasks can be chained together into Plans (not covered today).
@olend

---

#### Anatomy of a Task

@ul[](false)
- Reside in $module\Tasks
- Task file (PowerShell, Ruby, Bash, etc)
- Metadata .json file [optional]
@ulend

```
C:\gits\kms_win [master ≡]> tree /F tasks
  C:\GITS\KMS_WIN\TASKS
    ├──slmgr_ato.json
    ├──slmgr_ato.ps1
    ├──slmgr_ipk.json
    └──slmgr_ipk.ps1
```
@[3-4](Example task 1.)
@[5-6](Example task 2.)

---

#### Anatomy of a Task cont...

```
C:\gits\control-repo > tree /F .\site\powershell_tasks
  C:\gits\control-repo\site\powershell_tasks
    ├── disablesmbv1.json
    ├── disablesmbv1.ps1
    ├── getkb.json
    ├── getkb.ps1
    ├── testconnection.json
    ├── testconnection.ps1
    ├── wuauclt.json
    ├── wuauclt.ps1
    └── logoffusers.ps1
```
@[2](Location for tasks directly in control-repo)
@[3-4](Example task 1.)
@[5-6](Example task 2.)
@[7-8](Example task 3.)
@[9-10](Example task 4.)
@[11](Example task 5.)

---

@title[Task Metadata (slmgr_ipk.json)]

<p><span class="slide-title">Task Metadata (slmgr_ipk.json)</span></p>

@size[.7em](Task metadata files describe task parameters, validate input, and control how the task runner executes the task.)

```json
{
    "puppet_task_version": 1,
    "description": "A task for installing a windows product key with slmgr.vbs.",
    "input_method": "powershell",
    "parameters": {
        "activation_key": {
            "description": "The activation key to be installed via slmgr.vbs /ipk.",
            "type": "String"
        }
    }
}
```

---

@title[Task File (slmgr_ipk.ps1)]

<p><span class="slide-title">Task File (slmgr_ipk.ps1)</span></p>

```powershell
Param(
    [Parameter(Mandatory=$True)]
    [String]$activation_key
)
cscript.exe C:\Windows\System32\slmgr.vbs /ipk $activation_key
```

---

#### Tasks Demo

@ol[](false)
- slmgr
- SMBv1
- testconnection
@olend

---

#### Upgrade PowerShell with Tasks & Chocolatey

![choco](assets/image/yodawg.jpg)

---

#### Prerequisites

@ol[](false)
- chocolatey installed
- package_inventory_enabled = true
@olend

---

#### Tasks & Chocolatey Demo

```
Inventory: inventory[certname] { facts.powershell_version = "4.0" }
Task parameters:
  action: upgrade
  name: powershell
  provider: chocolatey
  version: 5.1.14409.20180105
```

---

#### Puppet & DSC

@ol[](false)
- Manages all the bad parts about DSC.
  + DSC module distribution
  + Pull or Push Model
  + Version control
- Still requires PowerShell v5.0
@olend

---

#### DSC Modules?

![choco](assets/image/magnets.jpg)

---

#### DSC Modules

![choco](assets/image/dsc_mod.jpg)

---

#### Syntax

<p><span class="slide-title">Traditional DSC</span></p>

```
WindowsFeature IIS {
  Ensure = 'present'
  Name   = 'Web-Server'
}
```

<p><span class="slide-title">Puppet DSC</span></p>

```
dsc_windowsfeature {'IIS':
  dsc_ensure => 'present',
  dsc_name   => 'Web-Server',
}
```
---

<p><span class="slide-title">website demo</span></p>

```
dsc_windowsfeature {'IIS':
  dsc_ensure => 'present',
  dsc_name   => 'Web-Server',
}

dsc_windowsfeature {'AspNet45':
  dsc_ensure => 'present',
  dsc_name   => 'Web-Asp-Net45',
}

dsc_windowsfeature {'console':
  dsc_ensure => 'present',
  dsc_name   => 'Web-Mgmt-Console',
}

dsc_xwebsite {'Stop DefaultSite':
  dsc_ensure       => 'present',
  dsc_name         => 'Default Web Site',
  dsc_state        => 'Stopped',
  dsc_physicalpath => $defaultwebsitepath,
}

dsc_file {'tmp folder':
  dsc_ensure          => 'present',
  dsc_type            => 'Directory',
  dsc_destinationpath => $zippath,
}

dsc_xremotefile {'Download website Zip':
  dsc_destinationpath => $zipfile,
  dsc_uri             => $zipuri,
}

dsc_archive {'Unzip and Copy the website':
  dsc_ensure      => 'present',
  dsc_path        => $zipfile,
  dsc_destination => $destinationpath,
}

dsc_xwebsite {'LetsDoThis':
  dsc_ensure       => 'present',
  dsc_name         => $websitename,
  dsc_state        => 'Started',
  dsc_physicalpath => $destinationpath,
  dsc_bindinginfo  => [
                        {
                          protocol => 'http',
                          port     => 8080,
                        }
                      ],
}
```

@[1-4]
@[6-9]
@[11-14]
@[16-21]
@[23-27]
@[29-32]
@[34-38]
@[40-51]

---

![choco](assets/image/xwebsite_doc.jpg)

```
dsc_xwebsite {'LetsDoThis':
  dsc_ensure       => 'present',
  dsc_name         => $websitename,
  dsc_state        => 'Started',
  dsc_physicalpath => $destinationpath,
  dsc_bindinginfo  => [
                        {
                          protocol => 'http',
                          port     => 8080,
                        }
                      ],
}
```
@[6-11]

---

@size[.8em](your-content1)
@size[1.0em](your-content1)
@size[1.5em](your-content1)
@size[2.0em](your-content2)
@size[2.5em](your-content3)

---

@title[JavaScript Block]

<p><span class="slide-title">JavaScript Block</span></p>

```javascript
// Include http module.
var http = require("http");

// Create the server. Function passed as parameter
// is called on every request made.
http.createServer(function (request, response) {
  // Attach listener on end event.  This event is
  // called when client sent, awaiting response.
  request.on("end", function () {
    // Write headers to the response.
    // HTTP 200 status, Content-Type text/plain.
    response.writeHead(200, {
      'Content-Type': 'text/plain'
    });
    // Send data and end response.
    response.end('Hello HTTP!');
  });

// Listen on the 8080 port.
}).listen(8080);
```

@[1,2](You can present code inlined within your slide markdown too.)
@[9-17](Displayed using code-syntax highlighting just like your IDE.)
@[19-20](Again, all of this without ever leaving your slideshow.)

---?gist=onetapbeyond/494e0fecaf0d6a2aa2acadfb8eb9d6e8&lang=scala&title=Scala GIST

@[23](You can even present code found within any GitHub GIST.)
@[41-53](GIST source code is beautifully rendered on any slide.)
@[57-62](And code-presenting works seamlessly for GIST too, both online and offline.)

---

## Template Help

- [Code Presenting](https://github.com/gitpitch/gitpitch/wiki/Code-Presenting)
  + [Repo Source](https://github.com/gitpitch/gitpitch/wiki/Code-Delimiter-Slides), [Static Blocks](https://github.com/gitpitch/gitpitch/wiki/Code-Slides), [GIST](https://github.com/gitpitch/gitpitch/wiki/GIST-Slides)
- [Custom CSS Styling](https://github.com/gitpitch/gitpitch/wiki/Slideshow-Custom-CSS)
- [Slideshow Background Image](https://github.com/gitpitch/gitpitch/wiki/Background-Setting)
- [Slide-specific Background Images](https://github.com/gitpitch/gitpitch/wiki/Image-Slides#background)
- [Custom Logo](https://github.com/gitpitch/gitpitch/wiki/Logo-Setting) [TOC](https://github.com/gitpitch/gitpitch/wiki/Table-of-Contents) [Footnotes](https://github.com/gitpitch/gitpitch/wiki/Footnote-Setting)

---

## Go GitPitch Pro!

<br>
<div class="left">
    <i class="fa fa-user-secret fa-5x" aria-hidden="true"> </i><br>
    <a href="https://gitpitch.com/pro-features" class="pro-link">
    More details here.</a>
</div>
<div class="right">
    <ul>
        <li>Private Repos</li>
        <li>Private URLs</li>
        <li>Password-Protection</li>
        <li>Image Opacity</li>
        <li>SVG Image Support</li>
    </ul>
</div>

---

### Questions?

<br>

@fa[twitter](joeypiccola)

@fa[github](joeypiccola)

@fa[wrench](forge.puppet.com/jpi)

@fa[envelope-square](joey@joeypiccola.com)
