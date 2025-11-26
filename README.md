# Relatório Técnico
**Pentest**  
**Desafio CTF**

**Nome:** Yúri Cordeiro  
**Data:** 24/11/2025

---

## 1. Escopo
Este relatório documenta todos os testes práticos realizados em ambiente controlado para fins acadêmicos, no contexto da disciplina de Cibersegurança.  
O objetivo do teste foi identificar vulnerabilidades existentes no host `98.95.207.28` e coletar flags distribuídas pelo ambiente, simulando um ataque real de baixo a médio impacto.

---

## 2. Objetivo da pesquisa
Foi realizada uma avaliação de segurança (PenTest) para simular técnicas usadas por atacantes reais e compreender os impactos de falhas envolvendo:  
- Exposição de serviços  
- Falhas de configuração  
- Vulnerabilidades web  
- Vazamentos de credenciais  
- Escalada de privilégios  
- Exposição de arquivos internos  

O foco foi apenas acadêmico, sem qualquer intenção de causar danos ao ambiente.

---

## 3. Contato
- Ambiente educacional CTF (Laboratório de segurança)  
- Responsável técnico: José Menezes  
- Contato: fornecido internamente pela Kensei Cybersecurity  
- Host analisado: `98.95.207.28`

---

## 4. Declaração de Limite de Responsabilidade e Confidencialidade
Todos os testes foram realizados com autorização explícita e em ambiente criado exclusivamente para estudo.  
Nenhuma ação teve como objetivo prejudicar sistemas reais, violar políticas ou causar interrupção de serviços.

---

## 5. Data em que os testes foram feitos
Os testes ocorreram entre: 17 e 22 de Novembro de 2025

---

## 6. Introdução e Descrição do Ambiente
O host analisado (`98.95.207.28`) faz parte de um laboratório prático da Kensei destinado ao estudo de vulnerabilidades comuns em servidores web, FTP e bancos de dados.

O ambiente contém:  
- Um servidor Apache  
- Um serviço FTP aberto com login anônimo  
- Um phpMyAdmin exposto  
- Diretórios sensíveis  
- Banco MySQL com dados fictícios  
- Aplicação web vulnerável a múltiplos ataques  

O objetivo é treinar o aluno para identificar e explorar vulnerabilidades reais encontradas no dia a dia de pentesters.

---

## 7. Detalhamento dos Dados do Site e Subdomínios
Durante o reconhecimento inicial, foram identificados os seguintes serviços principais:

| Porta | Serviço | Descrição |
|-------|--------|-----------|
| 21    | FTP    | Login anônimo habilitado |
| 80    | Apache | Página principal, diretórios sensíveis e arquivos expostos |
| 8080  | Apache/phpMyAdmin | Acesso direto ao painel de banco de dados |
| 3306  | MySQL  | Banco contendo dados e flags |

Também foram descobertos diretórios sensíveis através do arquivo `robots.txt`:  
- /admin/  
- /backup/  
- /.git/  
- /config/  

Esses diretórios contribuíram diretamente para a descoberta de múltiplas flags.

---

## 8. Serviços e Tecnologias Identificadas
A partir do Nmap (`-sV -sC`):  
- Apache 2.x nas portas 80 e 8080  
- FTP (vsFTPd) com acesso anônimo  
- MySQL Server acessível via phpMyAdmin  
- Diretórios configurados sem proteção  
- Possível uso de engine PHP vulnerável a XSS e SQL Injection  

## 9. Resultados e Vulnerabilidades Encontradas

A seguir estão todas as vulnerabilidades exploradas e flags coletadas, em ordem cronológica, conforme o passo a passo.

### Lista completa de flags identificadas

Cada item contém:  
- Ação realizada  
- Ferramenta utilizada  
- Evidência encontrada  
- FLAG coletada  

---

### 1) FTP – Acesso Anônimo Habilitado
- **Ferramenta:** FTP  
- **Comando:** `ftp 98.95.207.28`  
- **User:** anonymous  
- **Password:** <Enter>  
- **Arquivo obtido:** `welcome.txt`  
- **FLAG →** `FLAG{ftp_4n0nym0us_4cc3ss}`

### 2) FTP – Senhas em arquivo exposto
- **Local:** `/confidential/passwords.txt`  
- **FLAG →** `FLAG{p4ssw0rd_f1l3_d1sc0v3ry}`

### 3) Exposição do robots.txt
- **URL:** `http://98.95.207.28/robots.txt`  
- **Diretórios sensíveis revelados**  
- **FLAG →** `FLAG{r0b0ts_txt_l34k4g3}`

### 4) Credenciais de Banco expostas
- **URL:** `http://98.95.207.28/config/database.php.txt`  
- **FLAG →** `FLAG{d4t4b4s3_cr3d3nt14ls_3xp0s3d}`

### 5) SQL Injection – Login sem senha
- **Payload usado:** `' OR '1'='1`  
- **FLAG →** `FLAG{sql_1nj3ct10n_m4st3r}`

### 6) XSS – Cookie exposto via Storage
- **Payload usado:** `"><script>alert(1)</script>`  
- **Local:** DevTools → Storage → Cookies  
- **FLAG →** `FLAG{xss_r3fl3ct3d_vuln3r4b1l1ty}`

### 7) Código Fonte – Flag oculta no HTML
- **URL:** `http://98.95.207.28/`  
- **Ação:** Ctrl+U  
- **FLAG →** `FLAG{b4s1c_s0urc3_c0d3_1nsp3ct10n}`

### 8) Escalada de Privilégio (phpMyAdmin)
- **Ação:** Inserir novo usuário admin na tabela users  
- **Login como superadmin em:** `http://98.95.207.28/admin.php`  
- **FLAG →** `FLAG{pr1v1l3g3_3sc4l4t10n_succ3ss}`

### 9) Tabela secreta com dados ocultos
- **Local:** Techcorp_db > secret_data > browse  
- **FLAG →** `FLAG{h1dd3n_d4t4_1n_d4t4b4s3}`

### 10) Descoberta de VIEW no MySQL
- **Local:** View sensitie_info  
- **FLAG →** `FLAG{v13w_d1sc0v3ry_4dv4nc3d}`

### 11) Vazamento de credenciais via /.git
- **Ferramenta:** msfconsole + curl  
- **FLAG →** `FLAG{g1t_cr3d3nt14ls_l34k}`

### 12) Descoberta de Painel Secreto
- **URL obtida após varredura de páginas administrativas:** `http://98.95.207.28/panel.php`  
- **FLAG →** `FLAG{s3cr3t_p4n3l_d1sc0v3ry}`

### 13) Exposição de arquivo de configuração via FTP
- **Arquivo:** `users.conf`  
- **FLAG →** `FLAG{c0nf1g_f1l3_r34d}`

---

## Resumo Geral das Vulnerabilidades

| Categoria | Vulnerabilidade | Impacto |
|-----------|----------------|---------|
| Acesso indevido | FTP anônimo | Alto |
| Exposição de arquivos | config/database.php.txt | Alto |
| Falha web | SQL Injection | Crítico |
| Falha web | XSS | Alto |
| Fraca segurança de diretórios | robots.txt | Médio |
| Vazamento de credenciais | .git-credentials | Crítico |
| Fraca autenticação | phpMyAdmin | Crítico |
| Escalada de privilégio | Conta superadmin | Crítico |
| Dados sensíveis no banco | Views, tabelas | Alto |

---

## 10. Conclusão

O ambiente analisado apresenta um conjunto de vulnerabilidades críticas, permitindo:  
- Acesso direto ao servidor via FTP sem senha  
- Exposição completa das credenciais do banco de dados  
- SQL Injection no login  
- XSS refletido  
- Acesso total ao phpMyAdmin  
- Modificação de usuários administrativos  
- Vazamento de dados sensíveis no banco de dados  
- Diretórios sem proteção contendo arquivos revelando configurações internas  
- Vazamento de credenciais via .git-credentials  

Todas as flags foram obtidas através de falhas reais, com impacto alto ou crítico, que em um ambiente produtivo poderiam resultar em:  
- Vazamento total de informações  
- Comprometimento de contas administrativas  
- Exposição de dados confidenciais  
- Controle total do servidor

---

## 11. Sugestões para Correção

1. **Desabilitar FTP ou exigir autenticação**  
   - FTP anônimo deve ser removido  
   - Aplicar FTPS com senha forte  

2. **Proteger diretórios sensíveis**  
   - /backup  
   - /config  
   - /.git  
   - Configurar: `Options -Indexes`  

3. Remover arquivos `.txt` com credenciais  
4. Proteger phpMyAdmin com firewall, VPN ou restrição de IP  
5. Implementar prepared statements no login para eliminar SQL Injection  
6. Sanitizar parâmetros refletidos para impedir XSS  
7. Remover repositórios Git acessíveis publicamente  
8. Política de senhas seguras e rotatividade periódica  
9. Logging e monitoramento de tentativas suspeitas


