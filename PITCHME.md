## @color[#2e75e8](PowerShell) + @color[#FE9F17](Puppet)
### Tasks, DSC, Chocolatey

---

## Agenda

- Puppet Tasks with PowerShell
- DSC
-

---

#### Puppet Tasks

@ol
- Tasks are single actions that you run on target machines in your infrastructure.
- Best for changes that aren’t about enforcing the state of machines.
- You can write tasks in any programming language that can run on the target nodes.
- Tasks can be chained together into Plans (not covered today).
@olend

---

#### Anatomy of a Task

@ul
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

@title[Task Metadata (slmgr_ipk.json)]

<p><span class="slide-title">Task Metadata (slmgr_ipk.json)</span></p>

@size[1.2em](Task metadata files describe task parameters, validate input, and control how the task runner executes the task.)

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

@fa[twitter gp-contact](@gitpitch)

@fa[github gp-contact](gitpitch)

@fa[medium gp-contact](@gitpitch)

---?image=assets/image/gitpitch-audience.jpg&opacity=100

@title[Download this Template!]

### Get your presentation started!
### [Download this template @fa[external-link gp-download]](https://gitpitch.com/template/download/black)

