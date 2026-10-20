# Keyra

Gerenciador de senhas para a família, com **criptografia de ponta a ponta feita no próprio navegador**. 
Criei o Keyra como projeto pessoal para aprender, na prática, como funcionam criptografia, derivação de chaves e controle de acesso, e ao mesmo tempo resolver um problema real: guardar as senhas da casa em um lugar só, de forma segura.
> **Projeto de estudo.** O código não passou por auditoria de segurança. Use por sua conta e risco e não guarde nada crítico sem entender como ele funciona.

---

## Funcionalidades

- **Senha-mestra** que protege todo o cofre. Ela nunca é salva nem enviada para lugar nenhum.
- **Criptografia AES-256-GCM** de cada senha antes de sair do navegador.
- **Separação por pessoa:** cada senha pode ser marcada como de "Eu", "Pai", "Mãe" ou de qualquer pessoa cadastrada, com filtro e cores por pessoa.
- **Cadastro de pessoas** sem limite: adicionar, renomear (atualiza todas as senhas da pessoa), reordenar e remover (as senhas não são apagadas, ficam como "Sem pessoa").
- **Categorias** (Bancos, E-mails, Streaming, Compras, Documentos…) com filtro.
- **Busca** por site, usuário, pessoa, categoria ou anotação.
- **Gerador de senhas fortes** com indicador de força.
- **Copiar** usuário e senha com um toque; senha escondida por padrão.
- **Compartilhamento com a família:** quem é convidado como Editor pode adicionar e editar; os demais ficam em modo leitura.
- **Tema claro e escuro** automático e layout pensado para celular.

## Como a segurança funciona

```
senha-mestra ──► PBKDF2 (SHA-256, 210.000 iterações, salt aleatório de 16 bytes)
                    │
                    ▼
              chave AES-256 (só na memória)
                    │
   senha + dados ──►│ AES-GCM (IV aleatório de 12 bytes por registro)
                    ▼
              texto cifrado ──► armazenamento
```

1. Na criação do cofre, é gerado um **salt aleatório** e a senha-mestra passa pelo **PBKDF2** para virar uma chave AES de 256 bits.
2. Um texto conhecido é cifrado com essa chave e salvo como **verificador**. Ao desbloquear, o app tenta decifrá-lo: se funcionar, a senha está correta. Assim a senha-mestra **nunca** é armazenada, nem em forma de hash.
3. Cada registro (título, usuário, senha, URL, categoria, pessoa e anotações) é cifrado com **AES-GCM** e um **IV novo** a cada gravação. O GCM também garante integridade: se o dado for alterado, a decifragem falha.
4. Até a **lista de pessoas** e o dono de cada senha ficam dentro da parte cifrada. Quem não tem a senha-mestra não vê nem de quem é cada registro.
5. A chave existe **só na memória**. Ao clicar em "Trancar" ou recarregar a página, ela é descartada.

Tudo usa a **Web Crypto API** nativa do navegador (`crypto.subtle`), sem bibliotecas de criptografia de terceiros.

## Tecnologias

- **HTML, CSS e JavaScript puro** (sem framework), em um único arquivo
- **Web Crypto API**: PBKDF2, AES-GCM e `crypto.getRandomValues`
- **Armazenamento compartilhado e identidade do usuário** do runtime de Artifacts do Claude (`window.claude.use("db")` e `window.claude.use("user")`)
- **Playwright** para testar os fluxos com um banco simulado

> ⚠️ A senha-mestra **não pode ser recuperada**. Se for esquecida, os dados ficam inacessíveis. É assim que tem que ser.

## 📚 O que aprendi

- A diferença entre **guardar um hash** e **derivar uma chave**, e por que um gerenciador de senhas não precisa (nem deve) guardar a senha-mestra.
- Por que o **salt** e o **número de iterações** do PBKDF2 importam contra ataques de força bruta.
- Por que o **IV nunca pode se repetir** no AES-GCM, e como o modo autenticado protege contra alteração dos dados.
- Como pensar em **metadados**: só cifrar a senha não basta se o nome da pessoa ou do site fica exposto.
- Como separar **autenticação** (quem pode acessar o armazenamento) de **criptografia** (quem consegue ler o conteúdo).

## Autor
## Gabriel Beato
https://keyras.netlify.app/
