# Mural da Turma: mini projeto de IA com Codex e n8n

Projeto prático do **Minicurso de IA da SheDevs**, feito por estudantes do curso de Análise e Desenvolvimento de Sistemas do IFPB, campus Esperança-PB, para alunas do 3º ano do curso técnico em Informática.

Neste projeto, você cria um **mural de recados** em que a turma toda escreve mensagens e todo mundo vê os recados aparecerem. O site e o backend são gerados **só com prompts**, usando o Codex, e o backend roda no n8n, sem precisar de banco de dados nem de chave de API.

## Sumário

- [O que você vai praticar](#o-que-você-vai-praticar)
- [Como funciona](#como-funciona)
- [Pré-requisitos](#pré-requisitos)
- [Estrutura do repositório](#estrutura-do-repositório)
- [Passo 2: Frontend](#passo-2-frontend)
- [Passo 3: Conectar frontend e backend](#passo-3-conectar-frontend-e-backend)
- [Passo 4: Debug](#passo-4-debug)
- [Passo 5: Ajustes de visual](#passo-5-ajustes-de-visual)
- [Passo 6: Melhorias](#passo-6-melhorias)
- [Compartilhando o mural com a turma](#compartilhando-o-mural-com-a-turma)
- [Problemas comuns](#problemas-comuns)
- [Cuidados](#cuidados)
- [Atividade de comparação: prompt inicial x prompt final](#atividade-de-comparação-prompt-inicial-x-prompt-final)
- [Créditos](#créditos)

## O que você vai praticar

- Escrever prompts com **persona, contexto, instrução bem definida e exemplo**.
- Gerar um backend (n8n) e um site (HTML, CSS e JavaScript) pedindo tudo ao Codex.
- Conectar o site ao backend por meio de uma API (`fetch`, JSON e CORS).
- Usar a IA para **investigar e corrigir erros**.
- **Revisar e testar** o código que a IA gera. Lembre-se: a IA é uma ferramenta, não substitui o seu raciocínio.

## Como funciona

```
Site (HTML/CSS/JS)  ──POST──►  n8n (Webhook → valida/censura → salva)
        ▲                                   │
        └─────────GET────────  n8n (Webhook → lê → devolve lista)
```

- **POST** `/mural-recados`: recebe `{nome, mensagem}`, valida, filtra palavras proibidas e salva o recado.
- **GET** `/mural-recados`: devolve os 20 recados mais recentes, do mais novo para o mais antigo.

Os recados ficam guardados dentro do próprio n8n (static data do workflow).

## Pré-requisitos

| O que | Como |
|---|---|
| Node.js | Instale a versão LTS em [nodejs.org](https://nodejs.org). |
| n8n | Rode `npx n8n` no terminal e abra `http://localhost:5678`. |
| Codex | Instalado e logado. Confira as condições de acesso gratuito, porque elas podem mudar. |
| Navegador | Qualquer um atual (Chrome, Firefox, Edge). |
| Imagens de referência | Um print de um slide do minicurso e a logo da SheDevs, para anexar no Passo 2. |

Abra o Codex em uma **pasta vazia** para começar o projeto.

## Estrutura do repositório

```
.
├── README.md
├── n8n/
│   └── workflow-mural.json     # gerado no Passo 1
├── mural/
│   ├── index.html              # gerado no Passo 2
│   ├── style.css
│   └── script.js
└── imagens/                    # slide e logo usados como referência visual
```

## Passo 2: Frontend

Anexe as imagens de referência (slide e logo) e cole este prompt no Codex:

```text
Aja como um desenvolvedor front-end e designer especialista em HTML, CSS e JavaScript puro.

Contexto: tenho um backend no n8n com a rota /mural-recados (POST envia {nome, mensagem}; GET devolve uma lista de {nome, mensagem, data}). O site é o "Mural da turma", feito para o minicurso de IA da SheDevs (IFPB, curso de Análise e Desenvolvimento de Sistemas, Esperança-PB). Anexei imagens dos nossos slides e da logo da SheDevs: o site deve ter a mesma identidade visual deles.

Instrução: crie a pasta mural/ com index.html, style.css e script.js, sem frameworks e sem bibliotecas.

ESTRUTURA DO index.html (use exatamente estes ids, porque o script depende deles):
- Topo com a logo da SheDevs à esquerda e o texto "Minicurso de IA" à direita.
- Seção inicial com o título "Ideias boas merecem ser compartilhadas.", um texto de apoio ("Deixe um recado, um incentivo ou aquela dica que pode alegrar o dia de alguém.") e um botão que rola até o formulário.
- Seção do formulário com o título "Escreva um recado", contendo: form#message-form, input#nome (máx. 60), textarea#mensagem (máx. 140), span#character-count (mostra "0/140"), p#form-status (mensagens de sucesso e erro), button#submit-button ("Colar no mural"). Abaixo do formulário, a dica: "💡 Até 140 caracteres: seja claro e gentil!".
- Seção do mural com o título "O mural da turma", span#message-total ("0 recados", "1 recado", "2 recados"...) e div#message-list onde ficam os recados.
- Rodapé: "Minicurso de IA, SheDevs. IFPB, Análise e Desenvolvimento de Sistemas, Esperança-PB, 2026."

ESTILO VISUAL (siga os slides anexados):
- Cores: coral #E8554E (principal), coral escuro #C0392B, azul-marinho #1B1B3A (títulos), cinza-azulado #2C3E50 (texto), fundo #F5F5F5, rosa-claro #FADBD8, linhas #DDDDDD. Para o botão, use um coral um pouco mais escuro (#D23F38) para garantir contraste com o texto branco.
- Fontes: serifada e negrito nos títulos (Noto Serif); Carlito (parecida com Calibri) no texto. Importe do Google Fonts.
- Os títulos das seções ficam em "faixas": retângulo coral com cantos arredondados (10px), texto branco, serifado, em maiúsculas, igual aos títulos dos slides.
- Primeira tela inspirada na capa dos slides: texto à esquerda e, à direita, um grande círculo coral que sai pela borda da tela, com a logo da SheDevs dentro. No celular, o círculo vira um detalhe rosa-claro no canto, sem a logo, para não atrapalhar a leitura.
- Logo da SheDevs: use a imagem anexa. Se não for possível, recrie em CSS um círculo com degradê laranja/rosa/vermelho e o texto branco "SheDev/s" em negrito arredondado.
- Formulário em um cartão branco com borda #DDDDDD e cantos de 14px. Campos com cantos de 8px; no foco, borda coral e um brilho suave.
- Recados como post-its levemente rotacionados (uma variável CSS --rotation entre -3deg e 3deg), com cores que se alternam: rosa-claro, pêssego (#FDEBD0), verde-claro (#D5F5E3) e cinza-azulado (#DFE6EE). Cada um tem um alfinete coral no topo e mostra a mensagem em destaque e, abaixo, o nome e a data.
- Rodapé com um círculo coral decorativo cortado no canto inferior direito, como nos slides.
- Layout responsivo: 3 colunas de recados no desktop, 2 no tablet e 1 no celular. Foco visível no teclado e respeito a prefers-reduced-motion.

COMPORTAMENTO (script.js):
- Deixe a URL da API em uma constante API_URL no topo do arquivo.
- Atualize o contador de caracteres enquanto a pessoa digita.
- Ao carregar a página, faça GET na API e desenhe os recados em #message-list. Troque aria-busy para "false" depois. Se a lista estiver vazia, mostre "Ainda não tem recados. Seja a primeira!".
- Ao enviar, faça POST com fetch (Content-Type application/json), desabilite o botão durante o envio, limpe o formulário, mostre "Recado colado no mural!" em #form-status com a classe "success" e recarregue a lista.
- Em caso de erro, mostre uma mensagem amigável em #form-status com a classe "error", dizendo o que fazer ("Não conseguimos enviar agora. Confira se o n8n está ativo e tente de novo.").
- Use textContent (nunca innerHTML) para exibir nome e mensagem, para evitar injeção de código.
- Cada post-it é um elemento com as classes: .note > .note-message (texto) e .note-meta > .note-author (nome) + a data formatada em pt-BR. Mensagens de "buscando" e "vazio" usam a classe .wall-placeholder.

Exemplo do visual esperado: as imagens anexas (slide do roteiro e logo da SheDevs).

Troque o valor de API_URL no script.js para http://SEU-IP:5678/webhook/mural-recados e me explique, em linguagem simples, como abrir o site e testar enviando um recado.
```
Depois, abra o `mural/index.html` no navegador, envie um recado e veja ele aparecer no mural.

## Passo 4: Debug

É normal algo dar errado (CORS, URL de teste em vez de produção, workflow desativado...). Em vez de desistir, peça ajuda à IA. Abra o console do navegador (**F12**, aba **Console**), copie o erro e cole no prompt:

```text
O navegador mostrou o erro abaixo ao enviar um recado. Investigue a causa, corrija o código e explique em linguagem simples o que estava errado:

[colar aqui o erro do console]
```

Se o import do JSON no n8n falhar, o mesmo vale: cole a mensagem de erro no Codex.

## Passo 5: Ajustes de visual

Um bom exercício para praticar **ajustar o prompt conforme a resposta recebida**:

```text
Compare o site com o anexo e ajuste o que estiver diferente: cores, fontes, faixas dos títulos e círculo da primeira tela. Liste o que você mudou.
```

```text
Teste o layout em largura de celular (375px) e corrija qualquer texto cortado, sobreposição ou rolagem horizontal.
```

## Passo 6: Melhorias

Desafios para quem terminar primeiro. Escolha um:

**Atualização automática do mural**

```text
No script.js, faça a lista de recados atualizar sozinha: busque na API a cada 5 segundos (setInterval), sem recarregar a página e sem piscar a tela. Pause a atualização quando a aba estiver oculta (document.hidden) e mantenha o texto digitado no formulário intacto durante as atualizações.
```

**Botão de curtir**

```text
Adicione um botão de "curtir" em cada recado. Isso exige uma nova rota no n8n (POST /curtir) e a atualização do contador na tela.
```

**Filtro por nome**

```text
Adicione um filtro por nome no site e uma rota no n8n que devolva só os recados daquela pessoa.
```

## Compartilhando o mural com a turma

A pessoa que vai hospedar o n8n (instrutora) faz o seguinte:

**1. Descobrir o IP na rede**

```bash
ifconfig      # Linux (procure a interface do Wi-Fi, ex.: wlp2s0)
ipconfig             # Windows (procure o "Endereço IPv4")
```

O IP tem a forma `192.168.x.x`. Ignore `127.0.0.1`, `docker0` e interfaces `br-...`.

**2. Iniciar o n8n**

```bash
n8n
```

O n8n já escuta em todas as interfaces por padrão. Para conferir:

```bash
ss -tlnp | grep 5678
```

Se aparecer `*:5678` ou `0.0.0.0:5678`, ele está aceitando conexões da rede.

**3. Liberar a porta no firewall (Ubuntu)**

```bash
sudo ufw status
```

Se estiver `active`, libere a porta 5678:

```bash
sudo ufw allow 5678/tcp
```

Se estiver `inactive`, o firewall não é um problema. No Windows, aceite o aviso do firewall em "Redes privadas" quando ele aparecer.

**4. Testar**

No próprio computador, usando o IP:

```bash
curl -i http://SEU-IP:5678/webhook/mural-recados
```

Depois, no celular conectado ao mesmo Wi-Fi, abra `http://SEU-IP:5678/webhook/mural-recados` no navegador. Se aparecer a lista de recados (mesmo vazia, `[]`), está tudo certo.

**5. Passar a URL para a turma**

Cada aluna coloca no `script.js` dela:

```js
const API_URL = 'http://SEU-IP:5678/webhook/mural-recados';
```

**6. Depois da aula**

```bash
sudo ufw delete allow 5678/tcp
```

E pare o n8n (Ctrl+C no terminal).

## Problemas comuns

| Sintoma | Causa provável | Solução |
|---|---|---|
| `Couldn't connect to server` no `curl` usando o IP | IP errado ou firewall bloqueando | Confira o IP do Wi-Fi com `ip -4 addr show` e libere a porta 5678 no `ufw`. |
| Funciona no notebook, mas não no celular | A rede isola os aparelhos (comum em redes de escola) | Ligue o **hotspot do celular** e conecte todo mundo nele. Confira o IP de novo. |
| `404` e "webhook is not registered" | Workflow não está ativo, ou foi usada a URL `/webhook-test/` | Ative o workflow (**Active**) e use `/webhook/`. |
| Erro de CORS no console | Cabeçalhos de CORS ausentes na resposta | Peça ao Codex para liberar o CORS no nó Webhook e nas respostas (Passo 4). |
| O recado envia, mas não aparece | O GET não atualizou, ou o workflow não está ativo | Recarregue a página, confira o workflow ativo e teste o GET com `curl`. |
| O IP mudou | Reconexão à rede ou troca para hotspot | Descubra o IP de novo e atualize o `API_URL`. |
| Um recado sumiu | Dois envios no mesmo instante podem se sobrescrever no static data | Raro em turmas pequenas. Para mais segurança, use uma Data Table do n8n ou o Google Sheets. |

## Cuidados

- Mantenha o computador que hospeda o n8n **ligado, na tomada e com o workflow ativo** durante toda a aula.
- Use essa configuração **só em rede local e só durante a aula**. Não exponha o n8n publicamente na internet sem proteção.
- O filtro de palavras do projeto é simples (5 palavras). Ele serve para demonstrar a ideia, não para moderar conteúdo de verdade.
- **Teste todos os prompts antes da aula.** O Codex pode gerar um JSON do n8n que precise de pequenos ajustes, e saber disso antes evita surpresas.
- **Sempre entenda, revise e teste o código** produzido pela IA.
