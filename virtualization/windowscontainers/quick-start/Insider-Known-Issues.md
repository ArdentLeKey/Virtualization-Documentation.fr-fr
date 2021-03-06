# <a name="known-issues-for-insider-builds"></a>Problèmes connus pour les builds Insider

## <a name="build-16237"></a>Build 16237

- L’isolation Hyper-V ne fonctionne pas correctement. Cette solution de contournement est nécessaire pour utiliser l’isolation Hyper-V dans la build 16237. Exécutez les commandes suivantes dans PowerShell :

```PowerShell
Get-ComputeProcess | ? IsTemplate -eq $true | Stop-ComputeProcess -Force
Set-ItemProperty 'HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Virtualization\Containers\' -Name TemplateVmCount -Type dword -Value 0 -Force
```

- Nano Server s’exécute maintenant en tant qu’utilisateur, de sorte que les commandes qui requièrent des privilèges d’administrateur échouent. L’inclusion d’une ligne telle que « RUN setx /M PATH » entraînera l’échec de la build. Pour ce scénario, vous pouvez utiliser cette solution :

```dockerfile
RUN setx PATH <path>
```
