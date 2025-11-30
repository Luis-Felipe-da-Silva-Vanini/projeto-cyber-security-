# projeto-cyber-security
Testes de Força Bruta com Medusa no Kali Linux: Um Guia Prático


Bom, o objetivo principal foi aprender na prática como ataques de força bruta podem ser perigosos. Em um mundo real, isso ajuda a identificar fraquezas antes que alguém mal-intencionado as explore. Tudo foi feito em máquinas virtuais isoladas, então nada de risco para redes reais. Se você está começando no mundo da cibersegurança, isso pode ser uma boa introdução.

## O Que Usei no Ambiente

- **Máquina Atacante:** Meu Kali Linux 2024.x, rodando tudo no VirtualBox.
- **Ferramenta Principal:** Medusa – é simples e eficaz para esses testes.
- **Máquina Alvo:** Metasploitable 2 e DVWA (aquele laboratório de vulnerabilidades web).
- **Rede:** Configurei como Host-Only e NAT para manter tudo isolado.
- **IP do Alvo:** Sempre usei `192.168.56.101` – fácil de lembrar.

Nada de complicação aqui, né? Só o básico para focar no que importa.

##  Wordlists Que Criei

Para não complicar, fiz uma wordlist bem simples com senhas comuns que as pessoas ainda usam (e não deveriam!). Incluí coisas como:

```
admin
123456
root
password
toor
guest
teste123
```

Salvei tudo em um arquivo chamado **wordlist.txt**. É claro que em cenários reais, você usaria listas maiores, como as do RockYou, mas aqui foi só para demonstrar.

## 🔧 Teste 1: Força Bruta no FTP

Comecei pelo FTP, que é um serviço antigo e muitas vezes mal configurado. O comando que usei foi:

```
medusa -h 192.168.56.101 -u admin -P wordlist.txt -M ftp
```

**O que aconteceu?** Se a senha estivesse na lista, o Medusa me avisava algo como: "Senha válida encontrada para o usuário **admin**: `password`." Caso contrário, só um "nada encontrado". É rápido e direto.

**Provas:** Tirei uns prints e salvei na pasta /img do repositório. Dá para ver o terminal mostrando o resultado.

## Teste 2: Força Bruta no Login Web (DVWA)

Aqui foi mais interessante, porque testei um login web. O DVWA é perfeito para isso, já que simula vulnerabilidades comuns. O comando ficou assim:

```
medusa -h 192.168.56.101 -u admin -P wordlist.txt -M http \
 -m FORM:"/dvwa/login.php:username=^USER^&password=^PASS^:Login failed"
```

**Resultado:** Funcionou! O Medusa encontrou credenciais válidas, como usuário **admin** com senha **password**. No DVWA, isso significa que consegui logar e acessar o painel. É assustador pensar que sites reais podem ser assim.

## Teste 3: Força Bruta no SMB

Por fim, ataquei o SMB, que é usado para compartilhamento de arquivos no Windows. Usei uma lista de usuários também (users.txt) para variar. Comando:

```
medusa -h 192.168.56.101 -U users.txt -P wordlist.txt -M smbnt
```

**O que deu:** Bingo! Encontrei um login válido: **msfadmin / msfadmin**. Isso mostra como senhas padrão podem ser um problema enorme.

 Como Validei Tudo

Não parei só no Medusa – quis confirmar que os acessos funcionavam de verdade.

- **FTP:** Usei o comando `ftp 192.168.56.101`, digitei "admin" como usuário e "password" como senha. Conectou sem problemas.
- **SMB:** Com `smbclient -L //192.168.56.101 -U msfadmin`, listei os compartilhamentos. Tudo certo.
- **DVWA:** Abri o navegador, fui para o login do DVWA e testei as credenciais. Entrei no sistema sem dificuldade.

Isso prova que o ataque não foi só teoria – funcionou na prática.

## ⚠️ O Que Descobri de Ruim

Esses testes revelaram uns problemas sérios, mesmo em um lab controlado:

- Senhas fracas são um convite para ataques.
- O FTP manda tudo em texto puro – qualquer um pode interceptar.
- O DVWA não tinha proteções como CAPTCHA ou limite de tentativas, o que facilita a vida de hackers.
- Serviços rodando sem necessidade, como SMB aberto.
- Credenciais padrão que vêm com sistemas antigos.

Em resumo, se você não cuida disso, é só questão de tempo até alguém entrar.

## 🛡️ Como Se Proteger

Não quero deixar ninguém na mão, então aqui vão dicas práticas para mitigar isso:

- **Senhas Fortes:** Use gerenciadores de senha e imponha regras de complexidade (letras, números, símbolos).
- **Bloqueio de Tentativas:** Configure sistemas para travar contas após algumas falhas (tipo fail2ban).
- **Substitua Serviços Inseguros:** Troque FTP por SFTP ou FTPS para criptografia.
- **Desative o Desnecessário:** Se não precisa de SMB, desligue.
- **Firewall e Acesso Limitado:** Restrinja quem pode acessar seus serviços.
- **Autenticação Extra:** Adote 2FA sempre que possível – um app no celular salva vidas.
- **Atualizações:** Mantenha tudo no latest patch.

Seguindo isso, você reduz o risco drasticamente.

## 📚 O Que Aprendi

Foi uma experiência olho-aberto! Esses testes com Medusa mostraram como um ataque simples pode derrubar defesas ruins. Em um ambiente controlado, é educativo, mas no mundo real, reforça a importância de boas práticas de segurança. Se você testar isso, lembre-se: só em labs isolados e com permissão!
