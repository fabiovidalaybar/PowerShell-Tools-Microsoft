# [cite_start]Procedimientos Microsoft: PowerShell
**Autor:** Fabio Andrés Vidal Aybar [cite: 6, 7]

Este repositorio contiene una guía detallada sobre la gestión de módulos, conexiones y procedimientos específicos para la administración de entornos Microsoft mediante PowerShell[cite: 8].

---

##Módulos 

###Notas importantes[cite: 16]:
* Reinstalar módulos no corrompe los archivos; de hecho, sirve para actualizarlos[cite: 17].
* Se recomienda ejecutar los códigos en **PowerShell 7** por ser una versión más avanzada; de lo contrario, utilizar la versión 5[cite: 18].

### Instalación de módulos 
*Para instalar módulos, es imprescindible contar con los "gestores de paquetes" (**PowerShellGet** y **PackageManagement**) instalados y actualizados[cite: 20]. 

*Antes de esto, el motor **NuGet** debe estar actualizado para permitir la comunicación con repositorios externos[cite: 21, 22]:

```powershell
# 1. Instalar el motor NuGet [cite: 23]
```Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force```

# 2. Actualizar gestor de paquetes [cite: 24]
```Install-Module -Name PowerShellGet -Force -AllowClobber -SkipPublisherCheck```
