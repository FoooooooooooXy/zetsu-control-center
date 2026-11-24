Zetsu Control Center (Experimental)

Software desktop em Python 100% stdlib para controle, telemetria e configuração do mouse Gamer Ninja Zetsu (ZTN3311BT / PAW3311) no Windows 10/11 x64, sem drivers de terceiros nem dependências externas em tempo de execução.

Aviso: Projeto experimental fruto de engenharia reversa do protocolo USB HID (0xFF01) e telemetria Bluetooth LE. Os executáveis distribuídos ainda não possuem assinatura digital Authenticode; utilize por sua conta e risco e faça backup dos seus perfis.

Recursos Principais
Comunicação nativa Windows (zetsu_core.py):
Cabo USB (0xA8A4:0x2255) e Dongle 2.4 GHz (0xA8A5:0x2255) via coleção vendor-defined MI_02 (0xFF01:0x0010) usando ctypes + DLLs do sistema (hid.dll, setupapi.dll, kernel32.dll, cfgmgr32.dll, BluetoothAPIs.dll), sem exigir privilégios administrativos.
Bluetooth LE (0x2717:0x32B0): leitura de bateria via PnP Windows e GATT 0x180F / 0x2A19.
Configuração completa do mouse (zetsu_app.py):
1 a 6 estágios de DPI (200–12000), régua interativa multi-estágio e presets rápidos (FPS Competitivo, Uso Diário Equilibrado, Autonomia Máxima).
Polling rate (125 / 250 / 500 / 1000 Hz), debounce (2–30 ms), sleep (1–60 min), flags do sensor PAW3311 (Ripple Control, Angle Snapping, Motion Sync), direção do scroll, wakeup/LED ao mover e controle de LED (0 Off, 1..5 cores/canais, 6 efeito Fade).
Remapeamento dos 6 botões e exportação/importação de perfis .json em %LOCALAPPDATA%\ZetsuControl\profiles.
Bandeja do Windows e alertas (zetsu_tray.py):
Ícone nativo na área de notificação (Shell_NotifyIconW), confirmação ao fechar janela (minimizar para a bandeja ou encerrar, com opção Lembrar escolha em %LOCALAPPDATA%\ZetsuControl\ui_prefs.json).
Pop-ups nativos para bateria baixa (<= 15%) e alternância entre modos Cabo USB, Dongle 2.4 GHz e Bluetooth LE.
Assistente IA opcional (zetsu_llm*.py):
Suporte a múltiplos provedores gratuitos com adaptadores dedicados, fallback dinâmico, consentimento explícito antes de enviar telemetria sanitizada e armazenamento de API Keys criptografado localmente com Windows DPAPI (crypt32.dll).
Estrutura do Projeto
zetsu_core.py — Backend HID/BLE e protocolo 0xFF01
zetsu_app.py — Interface gráfica Tkinter e ponto de entrada CLI/GUI
zetsu_tray.py — Ícone da bandeja do Windows e notificações nativas (ctypes)
zetsu_llm_registry.py, zetsu_llm_secrets.py, zetsu_llm.py — Registro de modelos, cofre DPAPI e roteador LLM com fallback
test_llm_integration.py — Suíte de testes unitários offline (sem escrita no hardware)
zetsu.spec, version_info.txt, installer.iss, build_windows.ps1, requirements-build.txt — Automação de build PyInstaller + Inno Setup 6
CHANGELOG.md — Histórico de alterações por versão
Executando a partir do Código-Fonte

Requer Windows 10/11 x64 e Python 3.12+ com Tkinter (nenhum pacote pip externo necessário em runtime):

powershell
python zetsu_app.py

Opções de linha de comando:

--version — exibe a versão e encerra
--smoke-test — executa autodiagnóstico não destrutivo e grava %LOCALAPPDATA%\ZetsuControl\smoke_test.json
--no-tray / --exit-on-close — inicia sem ícone na bandeja / encerra diretamente ao fechar
Rodar os testes offline
powershell
python test_llm_integration.py
Build para Distribuição (PyInstaller + Inno Setup)

Requer Inno Setup 6 instalado e ambiente de build com requirements-build.txt:

powershell
powershell -NoProfile -ExecutionPolicy Bypass -File ".\build_windows.ps1" -Version "1.0.0"

Os artefatos são gerados fora do controle de versão em:

dist\ZetsuControl\ZetsuControl.exe (onedir)
release\ZetsuControl-v1.0.0-win64-portable.zip (versão portátil)
release\ZetsuControl_Setup.exe (instalador Windows)
Dados do Usuário

Todos os dados locais ficam isolados em %LOCALAPPDATA%\ZetsuControl (nunca na pasta de instalação):

profiles\ — perfis .json
secrets.dpapi.json — API Keys protegidas por Windows DPAPI
ai_prefs.json e ui_prefs.json — preferências do Assistente IA e comportamento ao fechar
