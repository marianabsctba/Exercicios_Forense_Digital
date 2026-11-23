# Resolução do Exercício de Forense Digital

**Autor:** Jorge Luis Carneiro Junior ([@jorgecarneirojr](https://github.com/jorgecarneirojr))
**Exercício original:** [marianabsctba/Exercicios_Forense_Digital](https://github.com/marianabsctba/Exercicios_Forense_Digital)
**Artefato:** `Banco_de_Dados.zip` (6 bancos SQLite)

---

## Conteúdo

- [`RELATORIO_DFIR.md`](RELATORIO_DFIR.md) — relatório completo com inventário, achados por fonte, linha do tempo consolidada, correlações e conclusão.
- [`POST_LINKEDIN.md`](POST_LINKEDIN.md) — texto de divulgação do resultado.

## Resumo executivo

O conjunto de artefatos descreve a coordenação de entrega de um "pacote" obtido em mercado da dark web:

| Evidência | Achado |
|-----------|--------|
| Mensagens Base64 (contato "Corvo") | "entrega as 22h na doca 7" / "levo o pacote comigo" / "apaga a conversa depois" |
| Navegação | Acesso a `mercadonegro3xk2.onion` (Tor) |
| Download | `pacote.zip` = 2.048.000 bytes |
| Integridade | `backup.zip` = 2.048.000 bytes (mesmo tamanho) |
| Senhas MD5 | admin=`123456`, mariana=`forense`, corvo=`qwerty` |
| Localização | 4 fixes GPS na madrugada em Curitiba/PR |
| Exfiltração | Acesso a pastebin anônimo às 03:08 UTC |

## Integridade do artefato

```
SHA-256: e787c2158a8902286b83802d4158af2fd717c27e71b1f7393fb24d5d40f0beab
MD5:     86ce4f3969a48a76f6f33dfa5e2c43d2
```

## Reprodução

```bash
unzip Banco_de_Dados.zip -d bancos
for f in bancos/Banco\ de\ Dados/*.db; do
  echo "== $f"
  sqlite3 "$f" ".schema"
done

echo 'ZW50cmVnYSBhcyAyMmggbmEgZG9jYSA3' | base64 -d
echo 'bGV2byBvIHBhY290ZSBjb21pZ28=' | base64 -d

python3 -c "import hashlib; print(hashlib.md5(b'123456').hexdigest())"
python3 -c "import hashlib; print(hashlib.md5(b'forense').hexdigest())"
python3 -c "import hashlib; print(hashlib.md5(b'qwerty').hexdigest())"
```

> Dados simulados/educacionais. Nenhum dado real de terceiros foi analisado.
