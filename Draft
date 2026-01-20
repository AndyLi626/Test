@echo off
setlocal EnableExtensions EnableDelayedExpansion

set "MATCH_1=EnhancedRTPLPortfolioWorker"
set "MATCH_2=ERTPLDailyWorker"

REM log next to this bat
set "LOG_DIR=%~dp0"

for /f %%i in ('powershell -NoProfile -Command "Get-Date -Format yyyyMMdd_HHmmss"') do set "TS=%%i"
set "LOG_FILE=%LOG_DIR%StopErtplWorkers_%COMPUTERNAME%_%TS%.log"

REM test write permission (this is the key)
> "%LOG_FILE%" (
  echo ==== StopErtplWorkers %date% %time% ====
  echo ScriptDir: %LOG_DIR%
  echo Match_1  : %MATCH_1%
  echo Match_2  : %MATCH_2%
) 2>nul

if not exist "%LOG_FILE%" (
  echo [FATAL] Cannot create log in "%LOG_DIR%". No write permission or path not accessible.
  exit /b 2
)

REM find+kill using PowerShell CIM (no wmic)
powershell -NoProfile -Command ^
"$m1='%MATCH_1%'; $m2='%MATCH_2%';" ^
"$ps=Get-CimInstance Win32_Process -Filter ""Name='java.exe'"" | Where-Object { $_.CommandLine -like ""*$m1*"" -or $_.CommandLine -like ""*$m2*"" };" ^
"if(-not $ps){ 'NO_MATCH' } else { $ps | ForEach-Object { 'KILL PID='+$_.ProcessId; Stop-Process -Id $_.ProcessId -Force -ErrorAction Continue } }" ^
>> "%LOG_FILE%" 2>&1

echo [INFO] Log: "%LOG_FILE%"
exit /b 0
