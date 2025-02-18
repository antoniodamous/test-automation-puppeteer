# Teste de Automação com Puppeteer

## 📌 Objetivo do Teste

Teste de automação de drag and drop de e-mail's da pasta Spam para a Caixa de Entrada.

---

## 1. Requisitos

- [Node.js](https://nodejs.org/)
- [Google Chrome](https://www.google.com/intl/pt-BR/chrome/)
- [Puppeteer](https://pptr.dev/)

## 2. Instalação e Configuração

Obs: Verifique se a conta do Gmail está logada com seu perfil. Depois pode encerrar a aplicação.

### 2.1 Instalar Puppeteer
```bash
npm install puppeteer
```

### 2.2 Iniciar o Chrome com Depuração Remota
Para conectar o Puppeteer a um navegador já aberto, inicie o Chrome com a opção de depuração remota ativada conforme exemplo abaixo. 
Obs:
- O caminho do executável pode variar
- Após execução do código no terminal o Chrome será aberto.
- Certificar que na inicialização do Chrome esteja como "Continuar de onde parou".

Linux
```bash
/opt/google/chrome/google-chrome --remote-debugging-port=9222
```

Mac OS
```bash
/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --remote-debugging-port=9222
```
Windows
```bash
"C:\\Program Files\\Google\\Chrome\\Application\\chrome.exe" --remote-debugging-port=9222
```

### 2.3 Localizar o WebSocketDebuggerURL

Abrir uma nova aba e acessar `http://localhost:9222/json` para encontrar o `webSocketDebuggerUrl` da aba do Gmail.

Obs:
- Efetuar comando CRTL+F e localizar: "Spam".
- Copiar "ws://localhost:9222/devtools/browser/SEU_BROWSER_ID".
- Esse caminho será atualizado no código abaixo.

## 3. Código-Fonte Principal (`test.js`)

```javascript
const puppeteer = require('puppeteer');

(async () => {
    try {
        // Conecta ao navegador
        const browser = await puppeteer.connect({
            browserWSEndpoint: 'ws://localhost:9222/devtools/browser/SEU_BROWSER_ID' // Atualize com o WebSocketDebbuggerURL localizado em http://localhost:9222/json
        });

        // Lista todas as abas e seleciona a primeira (No Caso o Gmail)
        const pages = await browser.pages();
        const gmailPage = pages[0];

        // Aguarda o carregamento da página de e-mails
        await gmailPage.waitForSelector('table.F.cf.zt');

        // Seleciona o primeiro e-mail da lista
        const emailToMove = await gmailPage.$('table.F.cf.zt tr');
        if (!emailToMove) throw new Error("Nenhum e-mail encontrado na pasta atual.");

        // Simula passar o mouse sobre o e-mail
        await emailToMove.hover();

        // Pressiona Shift para simular uma possível seleção múltipla (opcional)
        await gmailPage.keyboard.down('Shift');

        // Aguarda a Caixa de Entrada aparecer no menu lateral
        const inbox = await gmailPage.$('div[aria-label="Caixa de entrada"]');
        if (!inbox) throw new Error("Caixa de entrada não encontrada.");

        // Move a posição do e-mail e da Caixa de Entrada
        const emailBox = await emailToMove.boundingBox();
        const inboxBox = await inbox.boundingBox();
        if (!emailBox || !inboxBox) throw new Error("Falha ao obter as coordenadas dos elementos.");

        // Simula o arrastar e soltar do mouse
        await gmailPage.mouse.move(emailBox.x + emailBox.width / 2, emailBox.y + emailBox.height / 2);
        await gmailPage.mouse.down(); // Segura o clique
        await gmailPage.mouse.move(inboxBox.x + inboxBox.width / 2, inboxBox.y + inboxBox.height / 2, { steps: 10 }); // Move para a Caixa de Entrada
        await gmailPage.mouse.up(); // Solta o clique

        console.log('Email movido para a Caixa de Entrada!');

        // Fecha o navegador
        await browser.close();
    } catch (error) {
        console.error("Erro encontrado:", error);
    }
})();
```

## 4. Execução do Script

Salvar arquivo `test.js` na máquina local
```bash
node test.js
```


## 🙇🏻‍♂️ Apredizado
- [Documentação Puppeteer](https://pptr.dev/category/introduction)
- [Documentação Javascript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference)
- [DOM](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model)
- [Como conectar-se a um navegador existente](https://pptr.dev/#?product=Puppeteer&version=v5.5.0&show=api-puppeteerconnectoptions)
- [Seletores no Puppeteer](https://pptr.dev/#?product=Puppeteer&version=v5.5.0&show=api-pagewaitforselectorselector-options)
- [Chrome](https://developer.chrome.com/docs/devtools?hl=pt-br)
- [Seletore no CSS](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_selectors)


## 🔗 Links

<p align="center">
 
 <a href="https://www.linkedin.com/in/antoniodamous" alt="Linkedin">
  <img src="https://img.shields.io/badge/-Linkedin-0A66C2?style=for-the-badge&logo=Linkedin&logoColor=FFFFFF&link=https://www.linkedin.com/in/antoniodamous"/> 
 </a>

 </p>
 
## 💻 Autor<br>

<center>
      <a href="https://github.com/antoniodamous"> <center>
       <p align="center"><img src="https://github.com/antoniodamous.png" width="100px;" />
        </a> </p>

<h3 align="center"> Developed by <a href="https://www.linkedin.com/in/antoniodamous/">Antônio Damous</a> and <a [href="https://cloudflare.com/](https://github.com/puppeteer)">Puppeteer</a> 🥋</h3>



---
