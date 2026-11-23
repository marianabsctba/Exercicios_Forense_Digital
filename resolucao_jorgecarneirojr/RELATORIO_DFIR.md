# Relatório de Análise Forense Digital (DFIR)

**Exercício:** Banco de Dados — Mariana B S (LinkedIn)
**Fonte:** `https://github.com/marianabsctba/Exercicios_Forense_Digital/blob/main/Banco_de_Dados.zip`
**Artefato analisado:** `Banco_de_Dados.zip`
**SHA-256 do ZIP:** `e787c2158a8902286b83802d4158af2fd717c27e71b1f7393fb24d5d40f0beab`
**MD5 do ZIP:** `86ce4f3969a48a76f6f33dfa5e2c43d2`
**Data da análise:** 2026-11-23
**Natureza dos dados:** simulados / educacionais (não há dados reais de terceiros)

---

## 1. Inventário de evidências

| # | Arquivo | Tabelas | Registros |
|---|---------|---------|-----------|
| 1 | `1_uso_apps.db` | `app_usage` | 8 |
| 2 | `2_mensagens.db` | `contatos`, `mensagens` | 3 / 5 |
| 3 | `3_chamadas.db` | `calls` | 6 |
| 4 | `4_navegacao.db` | `history`, `downloads` | 5 / 2 |
| 5 | `5_localizacao.db` | `fixes` | 4 |
| 6 | `6_integridade.db` | `arquivos`, `senhas_md5` | 4 / 3 |

Todos os bancos são SQLite 3.x (versão 3050004), UTF-8, íntegros.

---

## 2. Achados por fonte

### 2.1 Mensagens (`2_mensagens.db`)

Contatos: **Ana Paula Ribeiro** ("Ana"), **Desconhecido** ("Corvo"), **Bruno Tavares** ("Bruninho").

Mensagens decodificadas (Base64):

| ID | Contato | Direção | Conteúdo decodificado | Timestamp (UTC) |
|----|---------|---------|----------------------|-----------------|
| 1 | Ana | recebida | "Oi, tudo bem? Cade voce?" | 2026-05-26 12:53:20 |
| 2 | **Corvo** | recebida | **"entrega as 22h na doca 7"** | 2026-05-26 13:13:20 |
| 3 | **Corvo** | enviada | **"levo o pacote comigo"** | 2026-05-26 13:23:20 |
| 4 | Bruno | recebida | "bora jogar bola amanha?" | 2026-05-26 13:43:20 |
| 5 | **Corvo** | recebida | **"apaga a conversa depois"** | 2026-05-26 14:08:20 |

**Indicador forte:** comunicação cifrada em Base64 com instrução de entrega, confirmação de posse de "pacote" e pedido de destruição de evidência (anti-forense).

### 2.2 Chamadas (`3_chamadas.db`)

| ID | Número/Nome | Tipo | Duração | Timestamp (UTC) |
|----|-------------|------|---------|-----------------|
| 1 | Ana | efetuada | 312 s | 2026-05-25 09:06:40 |
| 2 | **Corvo** | recebida | 47 s | 2026-05-25 12:01:40 |
| 3 | Ana | recebida | 128 s | 2026-05-25 14:45:00 |
| 4 | **Corvo** | efetuada | 9 s | 2026-05-25 17:46:40 |
| 5 | Banco (11) 3003-0000 | perdida | 0 s | 2026-05-25 20:15:00 |
| 6 | **Corvo** | recebida | 205 s | 2026-05-25 23:15:00 |

**Observação:** contato "Corvo" com número inválido `0000-0000` — 4 interações em ~11h, incluindo chamada longa noturna (205 s).

### 2.3 Navegação e downloads (`4_navegacao.db`)

| ID | URL | Título | Visitas | Timestamp (UTC) |
|----|-----|--------|---------|-----------------|
| 1 | google.com | Google | 230 | 2026-05-26 01:46:40 |
| 2 | noticias.exemplo.com.br | Portal de Notícias | 44 | 2026-05-26 02:03:20 |
| 3 | **mercadonegro3xk2.onion** | Mercado (Tor) | 7 | 2026-05-26 02:28:20 |
| 4 | webmail.exemplo.com | Webmail | 61 | 2026-05-26 02:50:00 |
| 5 | **pastebin.exemplo/raw/xY12** | paste anônimo | 3 | 2026-05-26 03:08:20 |

Downloads:

| ID | Arquivo | Origem | Bytes | Timestamp (UTC) |
|----|---------|--------|-------|-----------------|
| 1 | fatura.pdf | webmail.exemplo.com | 182.340 | 2026-05-26 02:51:40 |
| 2 | **pacote.zip** | **mercadonegro3xk2.onion** | 2.048.000 | 2026-05-26 02:30:00 |

**Indicador forte:** acesso a serviço `.onion` (Tor) e download de `pacote.zip` (2 MB) — o mesmo tamanho do `backup.zip` listado em `6_integridade.db`. Correlação direta com a mensagem "levo o pacote comigo".

### 2.4 Localização (`5_localizacao.db`)

| ID | Latitude | Longitude | Precisão | Timestamp (UTC) |
|----|----------|-----------|----------|-----------------|
| 1 | -25.4284 | -49.2733 | 12 m | 2026-05-26 04:33:20 |
| 2 | -25.4411 | -49.2769 | 8 m | 2026-05-26 05:33:20 |
| 3 | -25.4950 | -49.2920 | 15 m | 2026-05-26 06:33:20 |
| 4 | -25.5163 | -49.1760 | 20 m | 2026-05-26 07:33:20 |

Coordenadas compatíveis com a região de **Curitiba/PR** (DDD 41 dos contatos). Deslocamento contínuo em direção sudeste ao longo da madrugada.

### 2.5 Uso de aplicativos (`1_uso_apps.db`)

| App | Lançamentos | Tempo total | Último uso (UTC) |
|-----|-------------|-------------|------------------|
| WhatsApp | 842 | 54.300.000 ms (~15h) | 2026-05-26 20:00:00 |
| Instagram | 613 | 41.200.000 ms | 2026-05-26 19:00:00 |
| Chrome | 455 | 30.100.000 ms | 2026-05-26 18:30:00 |
| Telegram | 388 | 22.750.000 ms | 2026-05-26 17:30:00 |
| Spotify | 301 | 61.800.000 ms | 2026-05-26 15:40:00 |
| Gmail | 190 | 8.400.000 ms | 2026-05-26 14:40:00 |
| Play Store | 97 | 3.100.000 ms | 2026-05-26 13:40:00 |
| YouTube | 540 | 72.900.000 ms | 2026-05-26 19:35:00 |

WhatsApp é o app mais usado (842 lançamentos) — coerente com o volume de mensagens. Telegram presente (canal alternativo).

### 2.6 Integridade e senhas (`6_integridade.db`)

Arquivos:

| ID | Nome | Tamanho | MD5 | Observação |
|----|------|---------|-----|------------|
| 1 | relatorio.docx | 45.120 | b27b4c86bc8f473a3d62f88233638063 | original |
| 2 | foto.jpg | 310.244 | 52ae1e75a725bbd73c15a627b2773ea6 | imagem válida |
| 3 | notas.txt | 128 | 0ea34f09793f24edcb50aa85686bb174 | **texto com senha em claro** |
| 4 | backup.zip | 2.048.000 | c2b0ab3ef4d7a3e5f291dae5507f4b39 | conferir integridade |

Senhas (MD5 quebrados por dicionário):

| Usuário | Hash MD5 | Senha em claro |
|---------|----------|----------------|
| admin | e10adc3949ba59abbe56e057f20f883e | **123456** |
| mariana | e1576f172c3b126ae7a6026e8995da85 | **forense** |
| corvo | d8578edf8458ce06fbc5bb76a58c5ca4 | **qwerty** |

**Achado crítico:** senhas fracas e reutilizáveis; `backup.zip` (2.048.000 bytes) tem exatamente o mesmo tamanho do `pacote.zip` baixado do `.onion`.

---

## 3. Linha do tempo consolidada (UTC)

| Data/Hora (UTC) | Evento | Fonte |
|-----------------|--------|-------|
| 2026-05-25 09:06:40 | Chamada efetuada p/ Ana (312 s) | chamadas |
| 2026-05-25 12:01:40 | Chamada recebida de Corvo (47 s) | chamadas |
| 2026-05-25 14:45:00 | Chamada recebida de Ana (128 s) | chamadas |
| 2026-05-25 17:46:40 | Chamada efetuada p/ Corvo (9 s) | chamadas |
| 2026-05-25 20:15:00 | Chamada perdida do Banco | chamadas |
| 2026-05-25 23:15:00 | Chamada recebida de Corvo (205 s) | chamadas |
| 2026-05-26 01:46:40 | Navegação Google | navegação |
| 2026-05-26 02:03:20 | Portal de Notícias | navegação |
| 2026-05-26 02:28:20 | Acesso ao mercado `.onion` (Tor) | navegação |
| 2026-05-26 02:30:00 | Download `pacote.zip` (2 MB) do `.onion` | downloads |
| 2026-05-26 02:50:00 | Acesso ao Webmail | navegação |
| 2026-05-26 02:51:40 | Download `fatura.pdf` | downloads |
| 2026-05-26 03:08:20 | Acesso a pastebin anônimo | navegação |
| 2026-05-26 04:33:20 | Fix GPS #1 (Curitiba) | localização |
| 2026-05-26 05:33:20 | Fix GPS #2 | localização |
| 2026-05-26 06:33:20 | Fix GPS #3 | localização |
| 2026-05-26 07:33:20 | Fix GPS #4 | localização |
| 2026-05-26 12:53:20 | Msg de Ana: "Oi, tudo bem?" | mensagens |
| 2026-05-26 13:13:20 | **Msg Corvo: "entrega as 22h na doca 7"** | mensagens |
| 2026-05-26 13:23:20 | **Resposta: "levo o pacote comigo"** | mensagens |
| 2026-05-26 13:43:20 | Msg de Bruno: "bora jogar bola amanha?" | mensagens |
| 2026-05-26 14:08:20 | **Msg Corvo: "apaga a conversa depois"** | mensagens |
| 2026-05-26 13:40–20:00 | Atividade de apps (WhatsApp até 20:00) | uso de apps |

---

## 4. Correlações e inconsistências

1. **Cadeia de evidências "pacote":**
   - `pacote.zip` (2.048.000 bytes) baixado do `.onion` às 02:30 UTC.
   - `backup.zip` em `6_integridade.db` tem **exatamente 2.048.000 bytes**.
   - Mensagem "levo o pacote comigo" às 13:23 UTC.
   - Instrução "entrega as 22h na doca 7" às 13:13 UTC.
   → Forte indício de que o arquivo baixado na dark web é o objeto da entrega.

2. **Comportamento anti-forense:** pedido explícito para "apagar a conversa depois" e uso de Base64 para ofuscar o conteúdo das mensagens.

3. **Comunicação cifrada/obfuscada:** duas mensagens em Base64 (`ZW50cmVnYSBhcyAyMmggbmEgZG9jYSA3` e `bGV2byBvIHBhY290ZSBjb21pZ28=`).

4. **Contato suspeito:** "Corvo" com número inválido `0000-0000`, 4 chamadas e 3 mensagens — principal interlocutor.

5. **Janela temporal:** atividade noturna (01:46–07:33 UTC) com deslocamento geográfico contínuo, seguida de mensagens diurnas coordenando entrega noturna (22h).

6. **Higiene de segurança ruim:** senhas `123456`, `qwerty`, `forense`; senha em texto claro em `notas.txt`.

7. **Possível exfiltração:** acesso a pastebin anônimo (`/raw/xY12`) às 03:08 UTC, após download do pacote — pode indicar publicação de dados.

---

## 5. Conclusão

O conjunto de artefatos descreve, de forma consistente, um cenário de **coordenação de entrega de um "pacote" obtido em mercado da dark web (Tor)**, com:

- **Download** de `pacote.zip` (2 MB) de `mercadonegro3xk2.onion`;
- **Comunicação ofuscada** (Base64) com o contato "Corvo";
- **Instrução de entrega** ("22h na doca 7") e confirmação de posse;
- **Tentativa de destruição de evidências** ("apaga a conversa depois");
- **Deslocamento geográfico** na região de Curitiba durante a madrugada;
- **Credenciais fracas** que permitiriam comprometimento das contas.

**Recomendações (contexto educacional):**
- Preservar hashes (SHA-256) de todos os bancos antes da análise.
- Documentar a cadeia de custódia.
- Correlacionar `pacote.zip` ↔ `backup.zip` por hash/tamanho.
- Aplicar política de senhas fortes e MFA.
- Monitorar tráfego Tor e downloads de origens anônimas.

---

## 6. Anexos — comandos de reprodução

```bash
# Integridade
sha256sum Banco_de_Dados.zip

# Estrutura
for f in bancos/Banco\ de\ Dados/*.db; do echo "== $f"; sqlite3 "$f" ".schema"; done

# Decodificação Base64
echo 'ZW50cmVnYSBhcyAyMmggbmEgZG9jYSA3' | base64 -d   # entrega as 22h na doca 7
echo 'bGV2byBvIHBhY290ZSBjb21pZ28=' | base64 -d       # levo o pacote comigo

# Quebra de MD5 (dicionário)
python3 -c "import hashlib; print(hashlib.md5(b'123456').hexdigest())"  # admin
python3 -c "import hashlib; print(hashlib.md5(b'forense').hexdigest())" # mariana
python3 -c "import hashlib; print(hashlib.md5(b'qwerty').hexdigest())"  # corvo
```
