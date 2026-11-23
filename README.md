# AES-256 em C

> Implementação didática de **criptografia simétrica AES-256-CBC**, com operações de cifragem e decifragem via terminal.

![C](https://img.shields.io/badge/C-A8B9CC?style=flat-square&logo=c&logoColor=1A1A1A)
![Security](https://img.shields.io/badge/área-criptografia-6D5EF5?style=flat-square)
![Status](https://img.shields.io/badge/status-educacional-8B80F9?style=flat-square)

## Objetivo

Explorar como funciona o AES por dentro, em vez de depender exclusivamente de uma biblioteca pronta. O código implementa primitivas de transformação dos blocos e uma interface de linha de comando para cifrar e decifrar mensagens.

**Este projeto é educacional, não uma ferramenta de criptografia pronta para proteger dados reais.** Há limitações de segurança e validação de entrada que devem ser corrigidas antes de qualquer uso de produção.

## O que há no código

| Componente | Implementação |
|:--|:--|
| Cifra de bloco | AES com chave de 256 bits e 14 rodadas |
| Operações internas | SubBytes, ShiftRows, MixColumns, AddRoundKey e transformações inversas |
| Modo de operação | CBC (Cipher Block Chaining) |
| Preenchimento | PKCS#7 |
| IV | 16 bytes obtidos de `/dev/urandom` |
| Interface | `enc` e `dec`, com entrada e saída hexadecimal |

Arquivos principais:

- [`main.c`](main.c) — implementação principal e CLI.
- [`main_comentada.c`](main_comentada.c) — versão comentada para estudo passo a passo.

## Compilação

Requer compilador C (por exemplo, GCC) em ambiente com `/dev/urandom`, como Linux.

```bash
gcc -std=c11 -O2 -Wall -Wextra main.c -o aes256
```

## Exemplo de uso

```bash
# Demonstração: 32 caracteres ASCII usados diretamente como chave
./aes256 enc "mensagem de teste" "0123456789abcdef0123456789abcdef"
```

O programa imprime o texto cifrado em hexadecimal. Para decifrar:

```bash
./aes256 dec "COLE_AQUI_O_HEX_GERADO" "0123456789abcdef0123456789abcdef"
```

O valor fornecido no último argumento **não passa por derivação de chave**: os primeiros 32 bytes são utilizados como chave, e o restante é preenchido com zeros se a entrada for curta. O exemplo acima **não é uma recomendação de gerenciamento seguro de chaves**.

## Decisões e aprendizados

- **CBC:** cada bloco depende do bloco cifrado anterior, e a saída inclui o IV.
- **Padding:** entradas que não são múltiplas de 16 bytes recebem PKCS#7.
- **Transformações inversas:** a decifragem percorre as operações de AES na ordem correspondente.
- **Representação hexadecimal:** facilita inspecionar e transportar os bytes de saída durante os experimentos.

## Limitações de segurança conhecidas

- **Não há autenticação de integridade:** AES-CBC sem MAC permite manipulação de ciphertext; para aplicações reais, prefira criptografia autenticada, como **AES-GCM**, em biblioteca consolidada.
- **Não há KDF:** senhas curtas ou previsíveis não são transformadas com Argon2id, scrypt ou PBKDF2.
- **Argumentos de linha de comando expõem a chave** potencialmente ao histórico e a outros processos.
- **Buffers fixos e validação incompleta:** entradas longas, hexadecimais malformados ou ciphertexts inválidos podem provocar falhas de memória ou comportamento incorreto.
- **O padding é validado parcialmente** e não autentica dados; uma falha de padding não prova que a senha está errada.
- **Portabilidade limitada:** a geração de IV depende de `/dev/urandom`.

Essas limitações são parte do escopo de estudo e estão documentadas para evitar que o projeto seja confundido com uma solução segura de produção.

## Evoluções possíveis

- Adicionar testes de referência com vetores oficiais do AES (NIST).
- Validar rigorosamente tamanhos de entrada e formato hexadecimal.
- Usar APIs criptográficas auditadas para cenários reais.
- Documentar comparações com implementações de referência.

---

**Estudo de criptografia e programação de baixo nível.** Criado por [Talysson Decol](https://github.com/TalyssonDLF).
