# Conversor de Receitas

App (PWA) para Android que guarda receitas por tópicos e recalcula os ingredientes para
qualquer quantidade, sem resultados quebrados como "3,7 claras".

**Abrir:** https://renatobelmont.github.io/conversor-receitas/
No Chrome do Android, use ⋮ → *Instalar aplicativo*.

## Recursos

- Conversão **por rendimento** (quero 18 fatias) ou **por ingrediente** (tenho 5 claras)
- Itens contáveis nunca saem fracionados; quando o arredondamento pesa, o app mostra o peso equivalente em gramas
- Receitas divididas em **partes** (massa, recheio, cobertura…)
- **Duplicar** receita para criar variações
- **PDF**, impressão e envio como texto, sempre com as quantidades convertidas
- Backup em arquivo `.json` e no **Google Drive** ([configuração](docs/google-drive.md))
- Funciona offline; temas claro e escuro

## Arquivos

| Arquivo | Função |
|---|---|
| `index.html` | O app inteiro (HTML, CSS e JS, sem build) |
| `config.js` | ID do cliente Google para o backup no Drive |
| `sw.js` | Service worker (funcionamento offline e atualizações) |
| `manifest.json`, `icon-*.png` | Instalação como app |
| `vendor/jspdf.umd.min.js` | [jsPDF](https://github.com/parallax/jsPDF) 4.2.1 (MIT), usado para gerar os PDFs |
| `docs/google-drive.md` | Passo a passo da API do Google Drive |

A versão atual aparece no rodapé do app e em `APP_VERSION`, no `index.html`.
