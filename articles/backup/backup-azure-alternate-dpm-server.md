---
title: Wiederherstellen von Daten von einer Azure Backup Server-Instanz
description: Stellen Sie die Daten wieder her, die Sie in einem Recovery Services-Tresor auf einer beliebigen, bei diesem Tresor registrierten Azure Backup Server-Instanz gesichert haben.
author: kasinh
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: kasinh
ms.openlocfilehash: aaa2efa706822bee85dc867ad35bc312f4c700a1
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466898"
---
# <a name="recover-data-from-azure-backup-server"></a>Wiederherstellen von Daten von Azure Backup Server
Mit Azure Backup Server können Sie die Daten wiederherstellen, die Sie in einem Recovery Services-Tresor gesichert haben. Der entsprechende Prozess ist in der Azure Backup Server-Verwaltungskonsole integriert und ähnelt dem Wiederherstellungsworkflow für andere Azure Backup-Komponenten.

> [!NOTE]
> Dieser Artikel gilt für [System Center Data Protection Manager 2012 R2 mit UR7 oder höher](https://support.microsoft.com/en-us/kb/3065246), kombiniert mit dem [neuesten Azure Backup-Agent](https://aka.ms/azurebackup_agent).
>
>

So stellen Sie Daten von einer Azure Backup Server-Instanz wieder her:

1. Klicken Sie auf der Registerkarte **Wiederherstellung** der Azure Backup Server-Verwaltungskonsole auf **Externen DPM hinzufügen** (links oben auf dem Bildschirm).   
    ![Externen DPM hinzufügen](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Laden Sie die neuen **Tresoranmeldeinformationen** des mit der **Azure Backup Server**-Instanz verknüpften Tresors herunter, wählen Sie die Azure Backup Server-Instanz aus der Liste der beim Recovery Services-Tresor registrierten Azure Backup Server-Instanzen aus, und geben Sie die **Verschlüsselungspassphrase** für den Server an, dessen Daten wiederhergestellt werden.

    ![Anmeldeinformationen des externen DPM](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Sie können nur Daten zwischen Servern von Azure Backup Servern wiederherstellen, die bei demselben Sicherungstresor registriert sind.
   >
   >

    Sobald der externe Server von Azure Backup Server erfolgreich hinzugefügt wurde, können Sie die Daten vom externen Server von Azure Backup Server und vom lokalen Server von Azure Backup Server auf der Registerkarte **Wiederherstellung** durchsuchen.
3. Durchsuchen Sie die Liste der Produktionsserver, die durch den externen Server von Azure Backup Server geschützt werden, und wählen Sie die entsprechende Datenquelle aus.

    ![Durchsuchen des externen DPM-Servers](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. Wählen Sie nacheinander **Monat und Jahr** aus der Dropdownliste **Wiederherstellungspunkte**, das erforderliche **Wiederherstellungsdatum** der Erstellung des Wiederherstellungspunkts sowie die **Wiederherstellungszeit** aus.

    Im unteren Bereich wird eine Liste von Dateien und Ordnern angezeigt, die Sie durchsuchen und an einem beliebigen Ort wiederherstellen können.

    ![Wiederherstellungspunkte des externen DPM-Servers](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Klicken Sie mit der rechten Maustaste auf das entsprechende Element, und klicken Sie dann auf **Wiederherstellen**.

    ![Externe DPM-Wiederherstellung](./media/backup-azure-alternate-dpm-server/recover.png)
6. Überprüfen Sie die **Wiederherstellungsauswahl**. Überprüfen Sie Datum und Uhrzeit der wiederhergestellten Sicherungskopie sowie die Quelle, aus der die Sicherungskopie erstellt wurde. Wenn die Auswahl fehlerhaft ist, klicken Sie auf **Abbrechen** , und navigieren Sie zur Registerkarte "Wiederherstellung", um dort den richtigen Wiederherstellungspunkt auszuwählen. Wenn die Auswahl richtig ist, klicken Sie auf **Weiter**.

    ![Zusammenfassung zur externen DPM-Wiederherstellung](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Wählen Sie **An anderem Speicherort wiederherstellen**aus. **Durchsuchen** den richtigen Speicherort für die Wiederherstellung aus.

    ![Alternativer Speicherort für externe DPM-Wiederherstellung](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Wählen Sie die gewünschte Option aus: **Kopie erstellen**, **Überspringen** oder **Überschreiben**.

   * **Kopie erstellen** erstellt eine Kopie der Datei, falls ein Namenskonflikt besteht.
   * **Überspringen**: Bei einem Namenskonflikt wird die Datei nicht wiederhergestellt, sodass die ursprüngliche Datei bleibt.
   * **Überschreiben**: Bei einem Namenskonflikt wird die vorhandene Kopie der Datei überschrieben.

     Wählen Sie die entsprechende Option für **Sicherheit wiederherstellen** aus. Sie können die Sicherheitseinstellungen des Zielcomputers anwenden, auf dem die Daten wiederhergestellt werden, oder die Sicherheitseinstellungen, die für das Produkt zum Zeitpunkt der Erstellung des Wiederherstellungspunkts galten.

     Geben Sie an, ob eine **Benachrichtigung** gesendet wird, sobald die Wiederherstellung erfolgreich abgeschlossen ist.

     ![Benachrichtigungen für externe DPM-Wiederherstellung](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. Auf dem Bildschirm **Zusammenfassung** werden die bisher ausgewählten Optionen aufgelistet. Nach dem Klicken auf **Wiederherstellen** werden die Daten am entsprechenden lokalen Speicherort wiederhergestellt.

    ![Zusammenfassung der Optionen für die externe DPM-Wiederherstellung](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > Der Wiederherstellungsauftrag kann auf der Registerkarte **Überwachung** des Servers von Azure Backup Server überwacht werden.
   >
   >

    ![Überwachen der Wiederherstellung](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. Klicken Sie auf der Registerkarte **Wiederherstellung** des DPM-Servers auf **Externen DPM löschen**, um die Ansicht des externen DPM-Servers zu entfernen.

    ![Löschen des externen DPM](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>Problembehandlung bei Fehlermeldungen
| Nein. | Fehlermeldung | Schritte zur Problembehandlung |
|:---:|:--- |:--- |
| 1. |Dieser Server ist nicht bei dem Tresor registriert, der durch die Tresoranmeldeinformationen angegeben ist. |**Ursache:** Dieser Fehler wird angezeigt, wenn die ausgewählte Datei mit den Tresoranmeldeinformationen nicht zu dem Recovery Services-Tresor gehört, dem die Azure Backup Server-Instanz zugeordnet ist, auf der die Wiederherstellung versucht wird. <br> **Lösung:** Laden Sie die Anmeldeinformationsdatei für den Tresor aus dem Recovery Services-Tresor herunter, bei dem die Azure Backup Server-Instanz registriert ist. |
| 2. |Die wiederherzustellenden Daten sind nicht verfügbar, oder der ausgewählte Server ist kein DPM-Server. |**Ursache:** Es sind keine anderen Azure Backup Server-Instanzen bei dem Recovery Services-Tresor registriert, oder die Instanzen haben ihre Metadaten noch nicht hochgeladen, oder der ausgewählte Server ist keine Azure Backup Server-Instanz (mit Windows-Server oder Windows-Client). <br> **Lösung:** Wenn noch andere Azure Backup Server-Instanzen bei dem Recovery Services-Tresor registriert sind, müssen Sie sicherstellen, dass der neueste Azure Backup-Agent installiert ist. <br>Sind noch andere Azure Backup Server-Instanzen bei dem Recovery Services-Tresor registriert, warten Sie nach der Installation einen Tag, um den Wiederherstellungsprozess zu starten. Während der Nacht werden die Metadaten für geschützte Sicherungen in die Cloud hochgeladen. Die Daten sind dann für die Wiederherstellung verfügbar. |
| 3. |Es sind keine anderen DPM-Server bei diesem Tresor registriert. |**Ursache:** Bei dem Tresor, von dem aus die Wiederherstellung versucht wird, sind keine weiteren Server von Azure Backup Server registriert.<br>**Lösung:** Wenn noch andere Azure Backup Server-Instanzen bei dem Recovery Services-Tresor registriert sind, müssen Sie sicherstellen, dass der neueste Azure Backup-Agent installiert ist.<br>Sind noch andere Azure Backup Server-Instanzen bei dem Recovery Services-Tresor registriert, warten Sie nach der Installation einen Tag, um den Wiederherstellungsprozess zu starten. Während der Nacht werden die Metadaten für alle geschützten Sicherungen in die Cloud hochgeladen. Die Daten sind dann für die Wiederherstellung verfügbar. |
| 4. |Die angegebene Verschlüsselungspassphrase stimmt mit der dem folgenden Server zugeordneten Passphrase nicht überein: **\<Servername>** . |**Ursache:** Die beim Verschlüsseln der wiederherzustellenden Daten auf dem Server von Azure Backup Server verwendete Verschlüsselungspassphrase stimmt nicht mit der angegebenen Verschlüsselungspassphrase überein. Der Agent kann die Daten nicht entschlüsseln. Daher schlägt die Wiederherstellung fehl.<br>**Lösung:** Geben Sie genau dieselbe Verschlüsselungspassphrase an, die dem Server von Azure Backup Server zugeordnet ist, dessen Daten wiederhergestellt werden sollen. |

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die anderen häufig gestellten Fragen:

- [Gängige Fragen](backup-azure-vm-backup-faq.md) zu Azure VM-Sicherungen
- [Gängige Fragen](backup-azure-file-folder-backup-faq.md) zum Azure Backup-Agent
