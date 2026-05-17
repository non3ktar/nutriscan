# NutriScan PWA — Guia de Deploy

## O que é o NutriScan?
Aplicativo PWA instalável (Android e iOS) que:
- Usa a câmera do celular para fotografar alimentos (hortifrúti, grãos, carnes)
- Identifica o alimento via IA (Groq - Llama 3 Vision)
- Gera receita personalizada usando o alimento como ingrediente principal
- Exibe informações nutricionais completas (calorias, proteínas, carboidratos, fibras)
- Oferece análise de segurança para 5 perfis de saúde: diabéticos, hipertensos, obesos, intolerantes a glúten e lactose

## Arquivos
```
nutriscan-pwa/
├── index.html       ← App principal (tudo em um arquivo)
├── manifest.json    ← Configuração do PWA
├── sw.js            ← Service Worker (cache offline)
└── README.md        ← Este arquivo
```

> **Ícones**: Você precisará gerar `icon-192.png` e `icon-512.png`. Use ferramentas como https://realfavicongenerator.net

## Como fazer o deploy

### Opção 1 — Netlify (recomendado, gratuito)
1. Acesse https://netlify.com e crie uma conta
2. Arraste a pasta `nutriscan-pwa/` para o painel do Netlify
3. Seu app estará disponível em `https://SEU-NOME.netlify.app`
4. Configure a variável de ambiente ou **insira sua API Key do Groq** no `index.html` se necessário (ver abaixo)

### Opção 2 — Vercel
1. Instale: `npm i -g vercel`
2. Na pasta: `vercel --prod`

### Opção 3 — GitHub Pages
1. Suba os arquivos para um repositório GitHub
2. Ative GitHub Pages nas configurações do repo (branch main, pasta raiz)
3. URL: `https://SEU-USUARIO.github.io/NOME-REPO`

### Opção 4 — Servidor próprio (Apache/Nginx)
- Copie os arquivos para a pasta pública do servidor
- O servidor deve servir via **HTTPS** (obrigatório para câmera e PWA)

## Configuração da API Groq

O app usa a API gratuita do Groq. Para produção, você precisaria de um **backend proxy** para proteger sua API key caso queira esconder a chave dos usuários.

### Configuração simples (para testes ou uso pessoal)
Adicione sua API key do Groq diretamente no fetch em `index.html` (no cabeçalho `Authorization`):
```javascript
headers: {
  'Content-Type': 'application/json',
  'Authorization': 'Bearer SUA_API_KEY_DO_GROQ_AQUI'
}
```

### Configuração segura (produção)
Crie um endpoint de backend (Node.js, Python, etc.) que:
1. Recebe a imagem do frontend
2. Faz a chamada para a API Groq com a key no servidor
3. Retorna o resultado

### Netlify Functions (recomendado para produção em larga escala)
```javascript
// netlify/functions/analyze.js
exports.handler = async (event) => {
  const body = JSON.parse(event.body);
  const response = await fetch('https://api.groq.com/openai/v1/chat/completions', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${process.env.GROQ_API_KEY}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify(body)
  });
  const data = await response.json();
  return { statusCode: 200, body: JSON.stringify(data) };
};
```

## Instalação no celular

### Android (Chrome)
1. Acesse o URL do app no Chrome
2. Toque em "Instalar" no banner ou no menu (⋮) → "Adicionar à tela inicial"

### iOS (Safari)
1. Acesse o URL no Safari
2. Toque em compartilhar (□↑) → "Adicionar à Tela de Início"

## Recursos do PWA
- ✅ Instalável na tela inicial
- ✅ Ícone personalizado
- ✅ Tela cheia (sem barra do navegador)
- ✅ Cache offline para os arquivos estáticos
- ✅ Service Worker registrado
- ✅ Câmera nativa do dispositivo
- ✅ Adaptado para dispositivos móveis

## Custo estimado
- API Groq (Llama 3.2 90B Vision): **Totalmente Gratuito** (dentro dos limites do tier free do Groq)
- Custo por análise: US$ 0,00
