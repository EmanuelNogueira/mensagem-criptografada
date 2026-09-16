# Mensagem criptografada — desafio RSA

**Aluno:** Emanuel Nogueira  
**Disciplina:** Segurança e Auditoria de Sistemas  
**Destinatário:** professor Igor Luiz Oliveira de Souza

## Arquivo para avaliação

**[Baixar mensagem.enc](https://raw.githubusercontent.com/EmanuelNogueira/mensagem-criptografada/main/mensagem.enc)**

A mensagem foi criptografada com a chave pública RSA disponibilizada pelo professor. Para recuperar o texto, basta usar a chave privada correspondente e os parâmetros abaixo. Não foi criada uma senha adicional para a mensagem. Caso a própria chave privada esteja protegida por senha, o OpenSSL solicitará essa senha.

O texto original permanece localmente com o aluno e não faz parte deste repositório nem de seu histórico.

## Descriptografar com OpenSSL

Baixe `mensagem.enc` e abra o terminal na pasta onde ele foi salvo. Substitua `chave-privada.pem` pelo caminho da sua chave privada RSA em formato PEM:

```sh
openssl pkeyutl -decrypt -inkey chave-privada.pem -in mensagem.enc -out mensagem-recuperada.txt -pkeyopt rsa_padding_mode:oaep -pkeyopt rsa_oaep_md:sha256 -pkeyopt rsa_mgf1_md:sha256
```

O resultado será o arquivo de texto UTF-8 `mensagem-recuperada.txt`.

No PowerShell, se o OpenSSL não estiver no PATH, use o caminho da instalação. Exemplo:

```powershell
& 'C:\Program Files\OpenSSL-Win64\bin\openssl.exe' pkeyutl -decrypt -inkey .\chave-privada.pem -in .\mensagem.enc -out .\mensagem-recuperada.txt -pkeyopt rsa_padding_mode:oaep -pkeyopt rsa_oaep_md:sha256 -pkeyopt rsa_mgf1_md:sha256
```

### Se a chave privada estiver no formato OpenSSH

Se a primeira linha for `-----BEGIN OPENSSH PRIVATE KEY-----`, converta **uma cópia** para PEM. A chave original deve ser preservada.

Em Linux/macOS ou Git Bash:

```sh
cp /caminho/da/sua/chave_privada ./chave-privada.pem
chmod 600 ./chave-privada.pem
ssh-keygen -p -m PEM -f ./chave-privada.pem
```

No PowerShell:

```powershell
Copy-Item -LiteralPath 'C:\caminho\da\sua\chave_privada' -Destination .\chave-privada.pem
ssh-keygen -p -m PEM -f .\chave-privada.pem
```

O `ssh-keygen` pode solicitar a senha atual da chave e uma senha para a cópia convertida. Depois, execute o comando de descriptografia acima. A chave privada é usada apenas no computador do professor e não deve ser enviada ao repositório.

## Arquivos e parâmetros

| Arquivo | Finalidade |
| --- | --- |
| `mensagem.enc` | Mensagem criptografada, em formato binário, com 384 bytes. |
| `mensagem.enc.sha256` | SHA-256 do arquivo criptografado, para conferir o download. |
| `igor-ifba.pub` | Chave pública original do professor, em formato OpenSSH. |
| `igor-ifba.pem` | A mesma chave pública, convertida para PEM para uso com OpenSSL. |

- **Algoritmo:** RSA de 3072 bits, com preenchimento OAEP.
- **Hash OAEP:** SHA-256.
- **Hash MGF1:** SHA-256.
- **Rótulo OAEP:** vazio, padrão do OpenSSL.
- **Formato da saída:** binário, sem codificação Base64.
- **Impressão digital da chave pública:** `SHA256:RgjT2pmdYXuHx7XY1LG4gvxhLe5iWkA2IJ5qyRFsg1U`.
- **Origem da chave:** [arquivo publicado pelo professor](https://github.com/igorlosouza/auditoria-seguranca-sistemas-2026/blob/main/igor-ifba.pub).

As chaves públicas incluídas documentam o destinatário; elas não são necessárias para executar a descriptografia.

Para conferir a integridade do download em Linux/Git Bash:

```sh
sha256sum -c mensagem.enc.sha256
```

No macOS, use `shasum -a 256 -c mensagem.enc.sha256`.

No PowerShell, compare o resultado com o valor em `mensagem.enc.sha256`:

```powershell
(Get-FileHash .\mensagem.enc -Algorithm SHA256).Hash
```

## Procedimento de criptografia

O aluno converteu a chave pública para PEM e criptografou a mensagem local com:

```sh
openssl pkeyutl -encrypt -pubin -inkey igor-ifba.pem -in mensagem.txt -out mensagem.enc -pkeyopt rsa_padding_mode:oaep -pkeyopt rsa_oaep_md:sha256 -pkeyopt rsa_mgf1_md:sha256
```

`mensagem.txt` representa o original mantido localmente. Com esta chave e estes parâmetros, o limite de entrada é 318 bytes; a mensagem enviada respeita esse limite. A chave pública foi validada pelo OpenSSL e a conversão foi conferida contra a original.

O procedimento foi testado com um par de chaves temporário, incluindo a conversão de uma chave privada OpenSSH protegida por senha para PEM e a recuperação exata do texto. A descriptografia da entrega final depende da chave privada do professor.

Referências: [OpenSSL pkeyutl](https://docs.openssl.org/3.5/man1/openssl-pkeyutl/) e [OpenSSH ssh-keygen](https://man.openbsd.org/ssh-keygen).
