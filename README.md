# SSMSCustomExtension_VSIX
This repository provides VSIX files for installing SSMS Custom Extensions.

# Installation Instructions
## Prerequisites
- Windows 11
- SQL Server Management Studio 22.x

## 1. Download VSIX Files
Download the vsix.zip file from the “Releases” section and extract it to the device where you want to install it.
This procedure assumes that the files have been extracted to `C:\vsix`.

## 2. Running the installation script
Run the following script.
```
$vsixPath = "C:\vsix"

$VsixInstaller = "C:\Program Files\Microsoft SQL Server Management Studio 22\Release\Common7\IDE\VSIXInstaller.exe"
$Path = "C:\Program Files\Microsoft SQL Server Management Studio 22\Release\Common7\IDE\Ssms.exe"
$ssmsVersion = (Get-Item -LiteralPath $Path).VersionInfo.ProductVersion

$installFiles = @(  "CommonOptionsWindow.vsix", 
                    "IsolationLevelAutoInsert.vsix", 
                    "PerformanceResourceQuickPanel.vsix", 
                    "QueryChartPanel.vsix", 
                    "QueryEditorConnectionOverlay.vsix", 
                    "QueryEditorContextMenuExtensions.vsix", 
                    "WarningStatementDetector.vsix")

foreach ($file in $installFiles) {
    $vsixFile = Join-Path -Path $vsixPath -ChildPath $file

    Write-Host "Installing: $vsixFile"

    $process = Start-Process `
        -FilePath $VsixInstaller `
        -ArgumentList @(
            "/quiet",
            "/skuName:Ssms",
            "/skuVersion:$ssmsVersion",
            "`"$vsixFile`""
        ) `
        -Wait `
        -PassThru

    if ($process.ExitCode -ne 0) {
        throw "VSIX install failed. File=$vsixFile ExitCode=$($process.ExitCode)"
    }
}
```

Once the installation is complete, the extension will be deployed to `$env:LOCALAPPDATA\Microsoft\SSMS\22.0_xxxxxxxx\Extensions`.

## 3. Settings for displaying the menu
To display the extension menu, follow these steps.

1. Launch SSMS
2. [extensions] - [Customize Menu]
![Customize Menu](./img/01.png)
3. Uncheck [SSMS Extensions] in the [Extensions Menu], then click [Save and Restart]
![SSMS Extensions](./img/02.png)

When SSMS restarts, SSMS Extensions will appear as a separate menu.
![SSMS Extensions Menu](./img/03.png)


# Features available in this extension
This extension includes the following features.

1. Resource Quick Panel
2. Warning Detection
3. Transaction Isolation Level Auto / Manual Insert
4. Connection Information Overlay
5. Add a statement to verify query information
6. Query Chart Panel


## Resource Quick Panel

You can display the window by clicking “Resource Quick Panel” in the menu.  
![Resource Quick Panel](./img/04.png)

It runs queries on a regular basis and displays the results in the panel.  
![Resource Quick Panel](./img/05.png)

If the threshold is exceeded, a warning can also be displayed using a balloon tip.

## Warning Detection
If a query in the query editor contains `DROP TABLE` or `TRUNCATE TABLE`, a warning will be displayed.

![Warning Detection](./img/06.png)

## Transaction Isolation Level Auto / Manual Insert
Inserts the specified transaction isolation level into the first line of the currently displayed query editor.

![Transaction Isolation Level Auto / Manual Insert](./img/07.png)

## Connection Information Overlay
Displays information about the SQL Server to connect to in the Query Editor.
![Connection Information Overlay](./img/08.png)

## Add a statement to verify query information
Insert the DBCC statements “SET STATISTICS TIME, IO ON” into the query editor.
![Add a statement to verify query information](./img/09.png)

## Query Chart Panel
Run queries periodically and display a line graph.  
![Query Chart Panel](./img/10.png)