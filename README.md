# Keyra

Gerenciador de senhas local para Windows



## Backup

Em **Configurações → Backup**, o Keyra salva uma cópia do cofre (continua criptografada). Num PC novo, instale o
Keyra e use **"Já tem um backup? Restaurar cofre"** na primeira tela.

## Segurança

- O cofre fica em `%APPDATA%\Keyra\vault.keyra`, criptografado com AES-256-GCM.
- A chave é derivada da senha mestra com Argon2id (64 MiB, 3 passes) e só existe no processo principal enquanto o cofre está aberto.
- O cofre bloqueia por inatividade e quando o Windows é bloqueado ou suspenso.
- O que você copia é apagado da área de transferência depois de um tempo configurável.
- Não existe recuperação: sem a senha mestra, o cofre não abre.

##Gabriel Beato