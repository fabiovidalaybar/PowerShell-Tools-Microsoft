# Procedimientos Microsoft: PowerShell
**Autor:** Fabio Andrés Vidal Aybar

Este repositorio contiene una guía detallada sobre la gestión de módulos, conexiones y procedimientos específicos para la administración de entornos Microsoft mediante PowerShell[cite: 8].

---
```powershell

```
## Módulos 

### Notas importantes:
* Reinstalar módulos no corrompe los archivos; de hecho, sirve para actualizarlos.
* Se recomienda ejecutar los códigos en **PowerShell 7** por ser una versión más avanzada; de lo contrario, utilizar la versión 5.

### Instalación de módulos 
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
#### Instalar modulo Exchange
```powershell
Install-Module -Name ExchangeOnlineManagement -Force -AllowClobber -Scope CurrentUser

Import-Module ExchangeOnlineManagement
```
## Conexiones

> [!Nota]
> Usar principalmente powershell V7

#### Conectarse a Exchange
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

## Utilidades
### Comandos Base

Variables:
Tipo String
Nota: las comillas simples y dobles cambian el contexto, ojito con eso
$nombre_de_la_variable = "fabio"
Ejemplo:
$nombre = "Fabio"
$mensaje = "Hola $nombre" 
# El resultado será: Hola Fabio
$nombre = "Fabio"
$mensaje = 'Hola $nombre' 
# El resultado será: Hola $nombre (literalmente)

Tipo Lista
$nombre_de_la_variable = @(1, 2, 3, “A”)


  
