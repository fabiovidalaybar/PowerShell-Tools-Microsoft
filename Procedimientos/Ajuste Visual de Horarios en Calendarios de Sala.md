
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

