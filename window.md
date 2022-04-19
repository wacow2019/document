### WINDOWS 11 恢復WIN10的右鍵選單
```
變回win 10 選單

reg add "HKCU\Software\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}\InprocServer32" /f /ve 
taskkill /f /im explorer.exe & start explorer.exe


改成win 11 選單
reg delete "HKCU\Software\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}" /f 
taskkill /f /im explorer.exe & start explorer.exe
```