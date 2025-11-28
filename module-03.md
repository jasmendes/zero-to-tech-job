# module-03.md

## Módulo 3 — Projeto Realista (Parte 1)

### Objetivo
Criar um fluxo de desenvolvimento semelhante ao usado em equipas reais: branches, commits com significado, webhook do GitHub a notificar o Jenkins e build automático.

---

## 💡 O que vais aprender
- Criar branches e trabalhar com Git de forma profissional
- Configurar webhooks no GitHub
- Criar um job no Jenkins que dispara automaticamente com cada push
- Ver logs e entender o ciclo de CI

---

## 📌 1. Preparar o Projeto
1. Cria um repositório no GitHub chamado `projeto-realista`.
2. Adiciona um ficheiro simples, por exemplo `index.html`.
3. Faz clone do repositório para tua máquina.

```
git clone https://github.com/jaumendes/zero-to-tech-job.git
```


## 📌 2. Criar e Trabalhar em Branches
1. Garante que estás na branch `main`:

```
git checkout main
```


2. Cria uma branch de funcionalidade:
   
```
git checkout -b feature/say-hello
```

3. Edita o `index.html` e adiciona um pequeno texto.
4. Faz commit com mensagem significativa:
   
```
git add .
git commit -m "feat: add greeting paragraph"
```


5. Sobe a branch para o GitHub:
```
git push -u origin feature/say-hello
```

---

## 📌 3. Configurar Webhook no GitHub
1. No repositório → **Settings** → **Webhooks** → *Add Webhook*
2. Em **Payload URL**, coloca:
```
http://<JENKINS_URL>/github-webhook/
```
3. **Content Type:** `application/json`
4. Seleciona **Just the push event**
5. Clica **Add Webhook**

---

## 📌 4. Criar Job no Jenkins
1. No Jenkins → *New Item*
2. Nome: `projeto-realista-ci`
3. Escolhe *Freestyle Project* (ou Pipeline, se preferires)
4. Em **Source Code Management**:
   - Escolhe Git
   - Coloca o link do repositório GitHub
5. Em **Build Triggers**:
   - Ativa **GitHub hook trigger for GITScm polling**

Guarda o job.

---

## 📌 5. Testar o Build Automático
1. Faz nova alteração na tua branch e faz push.
2. Verifica no GitHub → Webhooks → entrega deve aparecer como *green*.
3. No Jenkins, o job deve arrancar automaticamente.
4. Abre os logs para verificar.

---

## 🎯 Resultado Final do Módulo 3
- Branches funcionais criadas
- Webhook configurado
- Jenkins a responder automaticamente a pushes
- Build CI a funcionar

---


# module-04.md

## Módulo 4 — Projeto Realista (Parte 2)

### Objetivo
Completar um pipeline real: build → deploy automático → notificação no Slack.

---

## 💡 O que vais aprender
- Criar um processo de deploy
- Usar ações pós-build do Jenkins
- Integrar Slack com Jenkins usando webhooks
- Entender notificações de sucesso e falha

---

## 📌 1. Adicionar Deploy Automático
No job do Jenkins:
1. Abre o job `projeto-realista-ci`
2. Vai a **Post-build Actions**
3. Adiciona um passo (ex.: *Execute Shell*) com um script simples:

```
#!/bin/bash
# Exemplo de deploy simples para uma pasta pública
cp -r * /var/www/html/projeto-realista/
```

Se preferires usar GitHub Pages:
```
git checkout gh-pages
cp -r * .
git add .
git commit -m "deploy automático"
git push origin gh-pages
```

---

## 📌 2. Criar Webhook no Slack
1. No Slack → *Manage Apps*
2. Procura **Incoming Webhooks**
3. Clica **Add New Webhook to Workspace**
4. Escolhe o canal (ex.: `#ci-cd`)
5. Copia a URL do webhook

---

## 📌 3. Adicionar Notificação no Jenkins
No job → **Post-build Actions** → *Execute Shell* (ou dentro do Jenkinsfile, se estiveres a usar Pipeline).

### Notificação em caso de sucesso:
```
curl -X POST -H 'Content-type: application/json' --data "{
  \"text\": \"Deploy realizado com sucesso! Build: ${BUILD_NUMBER}\"
}" <URL_DO_WEBHOOK_SLACK>
```

### Notificação em caso de falha:
Usa a secção de *Post-build* específica para falhas ou um bloco `post { failure {}}` em Jenkinsfile.

```
curl -X POST -H 'Content-type: application/json' --data "{
  \"text\": \"🚨 Deploy falhou! Build: ${BUILD_NUMBER}\"
}" <URL_DO_WEBHOOK_SLACK>
```

---

## 📌 4. Testar Tudo
1. Faz commit e push para uma branch
2. Jenkins deve:
   - Receber webhook
   - Fazer build
   - Executar deploy
   - Enviar notificação para Slack
3. Induz uma falha (linha inválida num script) e repete o teste

---

## 🎯 Resultado Final do Módulo 4
- Pipeline completo: CI + Deploy + Notificação
- Entendimento claro do fluxo DevOps
- Projeto realista pronto para portfólio

