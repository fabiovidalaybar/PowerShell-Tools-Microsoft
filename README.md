# ***Procedimientos Microsoft: PowerShell***
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
### Instalar módulo Sharepoint
```powershell
Install-Module -Name Microsoft.Online.SharePoint.PowerShell -Force
Import-Module Microsoft.Online.SharePoint.PowerShell
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
**Tipo String**  

> [!IMPORTANT]
> Las comillas simples y dobles cambian el contexto, ojito con eso.


$nombre_de_la_variable = "fabio"  
Ejemplo:  
$nombre = "Fabio"  
$mensaje = "Hola $nombre"   
El resultado será: Hola Fabio    
$nombre = "Fabio"    
$mensaje = 'Hola $nombre'     
El resultado será: Hola $nombre (literalmente)

**Tipo Lista**    
$nombre_de_la_variable = @(1, 2, 3, “A”)    


# **Procedimientos**

**Ajuste Visual de Horarios en Calendarios de Sala**  
1. El Problema: El "Bloqueo Gris" en Outlook  
Cuando un usuario intenta agendar una sala y ve que el fondo del calendario cambia de color (normalmente a gris) a partir de cierta hora, no siempre significa que la sala esté ocupada. Generalmente, esto indica que se ha alcanzado el límite de las Horas Laborales (Working Hours) configuradas en el buzón del recurso.  
2. El Comando de Solución  
Para corregir esto, se utiliza el cmdlet de Exchange Online que modifica la configuración visual del calendario:  
```PowerShell
Set-MailboxCalendarConfiguration -Identity "sala@dominio.com" -WorkingHoursStartTime 08:00:00 -WorkingHoursEndTime 20:00:00 -WorkingHoursTimeZone "SA Pacific Standard Time"
```
3. Desglose de Parámetros  
Parámetro	Función	Importancia  
-Identity	Identifica el correo electrónico de la sala.	Es el "sujeto" de la acción.  
**-WorkingHoursStartTime**:	Define cuándo se "ilumina" el calendario en la mañana.	Evita que el calendario se vea gris antes de empezar la jornada.  
**-WorkingHoursEndTime**:	Define cuándo vuelve a sombrearse el calendario en la tarde.	Al extenderlo (ej. a las 20:00), se eliminan las restricciones visuales para agendar tarde.  
**-WorkingHoursTimeZone**:	Asegura que el horario se base en la zona horaria local.	Para Chile, se usa "SA Pacific Standard Time" para evitar desfases.  


  
