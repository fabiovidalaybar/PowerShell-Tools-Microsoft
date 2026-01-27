# [cite_start]Procedimientos Microsoft: PowerShell
**Autor:** Fabio Andrés Vidal Aybar

Este repositorio contiene una guía detallada sobre la gestión de módulos, conexiones y procedimientos específicos para la administración de entornos Microsoft mediante PowerShell[cite: 8].

---

## Módulos 

### Notas importantes:
* Reinstalar módulos no corrompe los archivos; de hecho, sirve para actualizarlos.
* Se recomienda ejecutar los códigos en **PowerShell 7** por ser una versión más avanzada; de lo contrario, utilizar la versión 5.

### Instalación de módulos 
* Para instalar módulos, es imprescindible contar con los "gestores de paquetes" (**PowerShellGet** y **PackageManagement**) instalados y actualizados. 

* Antes de esto, el motor **NuGet** debe estar actualizado para permitir la comunicación con repositorios externos:

```powershell
# 1. Instalar el motor NuGet
```Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force```

# 2. Actualizar gestor de paquetes 
```Install-Module -Name PowerShellGet -Force -AllowClobber -SkipPublisherCheck```

* Uso de Scope 
El parámetro Scope permite saltarse restricciones de archivos del sistema instalando el módulo solo para el usuario actual:
```Install-Module -Name PowerShellGet -Scope CurrentUser -Force -AllowClobber```

-Exchange
```Install-Module -Name ExchangeOnlineManagement -Force -AllowClobber -Scope CurrentUser
Import-Module ExchangeOnlineManagement```
