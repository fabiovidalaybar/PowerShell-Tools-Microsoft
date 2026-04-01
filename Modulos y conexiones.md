## Módulos y conexiones

### Notas importantes:
* Reinstalar módulos no corrompe los archivos; de hecho, sirve para actualizarlos.
* Existen modulos que funcionan con V7 y otros solo con V5

### Instalación de módulos y conexiones
* Para instalar los módulos es necesario tener instalada y actualizadas las herramientas de PowerShellGet y PackageManagement, a estos se les llama “gestores de paquete”  y sirven precisamente para eso, para instalar los módulos. 
Pero antes de eso debes entender que para que powershell se pueda comunicar con repositorios externos debes tener actualizado “Nuget” ya que éste es el motor que permite esa comunicación. Por lo que esto es lo primero que debes instalar antes de todo:
```powershell
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Install-Module -Name PowerShellGet -Force -AllowClobber -SkipPublisherCheck
```
#### Scope: Scope sirve principalmente para saltarse restricciones de archivos del sistema:
```powershell
Install-Module -Name PowerShellGet -Scope CurrentUser -Force -AllowClobber
```
#### Exchange
```powershell
Install-Module -Name ExchangeOnlineManagement -Force -AllowClobber -Scope CurrentUser
Import-Module ExchangeOnlineManagement
```
```powershell
Connect-ExchangeOnline
```
## O
```powershell
Connect-ExchangeOnline -UserPrincipalName vedata@aurysconsulting.com
```
* Commando para desconectarse al final de la sesión:
```powershell
Disconnect-ExchangeOnline
```
### Instalar e importar módulo PnP
```powershell
Install-Module PnP.PowerShell
Import-Module PnP.PowerShell
```
* A finales de 2024 por razones de seguridad. Ahora, cada tenant debe registrar su propia aplicación en Entra ID para que el módulo funcione.
  para registrar tu propia aplicación en Entra ID
  ```powershell
  Register-PnPEntraIDAppForInteractiveLogin -ApplicationName "PnP PowerShell Aurys" -Tenant escribirnombredeltenant
  ```
  * ### Qué pasará ahora:
    * Se abrirá una ventana de inicio de sesión de Microsoft.
    * Entra con tus credenciales de admin.
    * Te aparecerá una pantalla de "Permisos solicitados". Debes marcar la casilla "Consentimiento en nombre de su organización" y hacer clic en Aceptar.
    * Una vez termine, la consola te mostrará un Client ID (un código largo de letras y números). Cópialo y guárdalo, lo necesitaremos ahora.

  * Ahora que tienes tu propia aplicación registrada, usa ese ID para conectar. Reemplaza TU-CLIENT-ID-AQUÍ por el código que obtuviste en el paso anterior:
```powershell
$siteUrl = "url de la pagina de SP a la que te quieres conectar"
$clientId = "TU-CLIENT-ID-AQUÍ"

Connect-PnPOnline -Url $siteUrl -Interactive -ClientId $clientId
```
### Sharepoint
> [!Nota]
> Para conectarse a SP, se debe usar PowerShell 5.1
```powershell
Install-Module -Name Microsoft.Online.SharePoint.PowerShell -Force
Import-Module Microsoft.Online.SharePoint.PowerShell
```

### Conectarse a Sharepoint
```powershell
Connect-SPOService -Url https://xxx-admin.sharepoint.com
```
