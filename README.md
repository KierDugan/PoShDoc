# PoShDoc

PoShDoc is a simple project that started as a script in
[PoShWarp](https://github.com/KierDugan/PoShWarp) to save duplicating the
documentation in both the doc-comments and the readme file.  It uses the object
returned by `Get-Help` to generate markdown.  The Commands section of the
PoShWarp documentation has been generated in this manner using
Moustache-inspired directives as follows:

```markdown
Commands
========

{% Select-WarpLocation %}
{% New-WarpLocation %}
{% Remove-WarpLocation %}
{% Get-WarpLocation %}
{% Repair-WarpMap %}
{% wd %}
```

<a id="BuildDoc-ps1"></a>
## BuildDoc.ps1

```
BuildDoc.ps1 -TemplateFile <String> [-OutputFile <String>] [-Modules <String[]>] [<CommonParameters>]
```

GitHub-Flavoured Markdown documentation converter for PowerShell command help.

### Description
This script uses the structure returned by `Get-Help` to generate GFM that can
be directly embedded in a README.md file.  A template file must be specified
that invokes the conversion at the appropriate places.  Consider the following
Markdown snippet as an example:

    # Commands
    {% MyFirstCommand %}
    {% MySecondCommand %}

This would result in Markdown containing a level 1 heading, followed by two
command descriptions generated by calling `Get-Help` on each one.  At present,
the output cannot be easily customised and the script *must* be able to resolve
the command names.  Module files can be imported by the script if necessary.

### Parameters
| Parameter    | Type     | Description                                                                          |
| :----------- | :------: | :----------------------------------------------------------------------------------- |
| TemplateFile | String   | Template file containing look-up directives of the form `{% <Command> %}`.           |
| OutputFile   | String   | Generated Markdown will be written into this file using UTF8 encoding without a BOM. |
| Modules      | String[] | List of module files (*.psm1) to import.                                             |


## Using the Script

This file was generated by the `BuildDoc.ps1` script using the `README.md.in`
file as the template.  Looking at that file provides a good example of how to
write a template.  The following command was used to generate this file:

```PowerShell
.\BuildDoc.ps1 -TemplateFile README.md.in -OutputFile README.md
```

Inside `README.md.in` is the directive `{% .\BuildDoc.ps1 %}` which leads to
the command description [shown below](#BuildDoc-ps1).  In this case, the target
command is a script file which is accessible from the directory in which the
command was invoked.  The script can therefore call the `Get-Help` cmdlet
without any issue.

However, if you want to document your own commands within a PowerShell module,
it is likely that the commands will not be available.  The module(s) containing
the commands must be specified so that `BuildDoc.ps1` can import them.  This
command will build the `README.md` file for PoShWarp:

```PowerShell
..\PoShDoc\BuildDoc.ps1 -TemplateFile README.md.in -OutputFile README.md `
    -Module .\PoShWarp.psm1
```

Any number of modules can be specified as a list, to support commands being
spread over multiple files.  It might be more pleasing to use `-Modules`
instead, but this is not a requirement as `-Module` is merely an alias of
`-Modules`.

## Suppressing Expansion

Any command name that cannot be resolved by `Get-Help` will lead to an error;
this is intentional as it is blindingly obvious that something has gone wrong.
However, in some cases (such as this file) it is necessary to prevent the script
from trying to evaluate a directive.  Any directive prefixed with a backslash
will be ignored with the backslash stripped off.  For example,

```markdown
This is an example directive \{% SomeNonExistentCommand %}.
```

will result in:

> This is an example directive {% SomeNonExistentCommand %}.

## Licence

The MIT License (MIT)

Copyright (c) 2014-2022 Kier Dugan

Permission is hereby granted, free of charge, to any person obtaining a copy of
this software and associated documentation files (the "Software"), to deal in
the Software without restriction, including without limitation the rights to
use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of
the Software, and to permit persons to whom the Software is furnished to do so,
subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS
FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR
COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER
IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN
CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
