# Boas Práticas de Performance — Páginas GitHub Pages

Referência para todas as páginas do projeto. Aplicar sempre antes de fazer deploy.

---

## 1. Preconnects no `<head>` (obrigatório)

Sempre adicionar no início do `<head>`, antes de qualquer `<link rel="stylesheet">`:

```html
<!-- CDN principal de imagens -->
<link rel="preconnect" href="https://assets.zyrosite.com" crossorigin>
<link rel="dns-prefetch" href="https://assets.zyrosite.com">

<!-- CDN de fontes Zyrosite/Hostinger -->
<link rel="preconnect" href="https://cdn.zyrosite.com" crossorigin>
<link rel="dns-prefetch" href="https://cdn.zyrosite.com">
```

**Atenção:**
- NUNCA usar `crossorigin="true"` — valor inválido que faz o preconnect ser ignorado
- NUNCA deixar `<link rel="preconnect">` sem `href` — tag inválida, remover

---

## 2. Fontes não-bloqueantes (obrigatório)

Substituir qualquer `<link rel="stylesheet">` de fontes por:

```html
<!-- Se usar Google Fonts diretamente: -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link rel="preload" href="https://fonts.googleapis.com/css2?family=SUA_FONTE&display=swap"
      as="style" onload="this.onload=null;this.rel='stylesheet'">
<noscript>
  <link href="https://fonts.googleapis.com/css2?family=SUA_FONTE&display=swap" rel="stylesheet">
</noscript>

<!-- Se usar CDN Zyrosite/Hostinger: -->
<link rel="preload" href="https://cdn.zyrosite.com/u1/google-fonts/font-faces?family=..."
      as="style" onload="this.onload=null;this.rel='stylesheet'">
<noscript>
  <link href="https://cdn.zyrosite.com/u1/google-fonts/font-faces?family=..."
        rel="stylesheet" referrerpolicy="no-referrer">
</noscript>
```

**Por quê:** `rel="stylesheet"` bloqueia toda a renderização da página (impacta FCP e LCP).

---

## 3. Preload da imagem hero/LCP (obrigatório)

Adicionar logo antes do `</head>` para a imagem principal (acima da dobra):

```html
<link rel="preload" as="image"
      href="URL_MOBILE_375px"
      imagesrcset="URL_375w 360w, URL_768w 720w, URL_1024w 945w, URL_1440w 1440w"
      imagesizes="(max-width: 500px) 800px, 100vw"
      fetchpriority="high">
```

E na tag `<img>` correspondente adicionar:
```html
<img ... loading="eager" fetchpriority="high" ...>
```

**Por quê:** Elimina o "atraso de renderização do elemento LCP" (era 540ms nesta página).

---

## 4. Meta Pixel / Facebook Pixel diferido (obrigatório)

NUNCA colocar o pixel bloqueante no `<head>`. Sempre usar:

```html
<!-- Meta Pixel Code (deferred) -->
<script>
window.addEventListener('load', function() {
  !function(f,b,e,v,n,t,s)
  {if(f.fbq)return;n=f.fbq=function(){n.callMethod?
  n.callMethod.apply(n,arguments):n.queue.push(arguments)};
  if(!f._fbq)f._fbq=n;n.push=n;n.loaded=!0;n.version='2.0';
  n.queue=[];t=b.createElement(e);t.async=!0;
  t.src=v;s=b.getElementsByTagName(e)[0];
  s.parentNode.insertBefore(t,s)}(window, document,'script',
  'https://connect.facebook.net/en_US/fbevents.js');
  fbq('init', 'SEU_PIXEL_ID');
  fbq('track', 'PageView');
});
</script>
<noscript>
  <img height="1" width="1" style="display:none"
       src="https://www.facebook.com/tr?id=SEU_PIXEL_ID&ev=PageView&noscript=1"/>
</noscript>
<!-- End Meta Pixel Code -->
```

**Por quê:** O fbevents.js pesa 137 KiB e bloqueia 136ms da thread principal se carregado normalmente.

---

## 5. Google Tag Manager / outros scripts externos

Qualquer script de terceiros (GTM, Hotjar, etc.) deve usar o mesmo padrão:

```html
<script>
window.addEventListener('load', function() {
  // colar o script aqui dentro
});
</script>
```

---

## 6. Imagens abaixo da dobra

Todas as imagens que não aparecem na tela imediatamente devem ter:
```html
<img ... loading="lazy">
```
(As páginas Zyrosite já fazem isso automaticamente nas exportações.)

---

## 7. Checklist antes de fazer deploy

- [ ] Preconnect para `assets.zyrosite.com` adicionado (com `crossorigin`, sem `="true"`)
- [ ] Preconnect para `cdn.zyrosite.com` adicionado (com `crossorigin`, sem `="true"`)
- [ ] Fontes carregando via `preload` + `onload` (não `rel="stylesheet"` direto)
- [ ] `<link rel="preconnect">` vazio removido (se existir)
- [ ] Preload da imagem hero adicionado antes de `</head>`
- [ ] `fetchpriority="high"` na tag `<img>` da hero
- [ ] Meta Pixel dentro de `window.addEventListener('load', ...)`
- [ ] Demais scripts de terceiros diferidos da mesma forma

---

## 8. O que NÃO dá para fazer no GitHub Pages

- Cabeçalhos HTTP customizados (`Cache-Control`, `Expires`) — plataforma não suporta
- Remoção de CSS não utilizado — CSS é gerado pelo builder Zyrosite
- Redução de DOM — estrutura gerada pelo builder

---

## Resultado obtido na página codigodivino.unidosnafe.site

- PageSpeed Mobile Desempenho: **80** (antes das otimizações)
- Acessibilidade: **94** (pode chegar a 100 corrigindo contraste dos botões CTA)
- Práticas Recomendadas: **100**
- SEO: **100**
- Pingdom: **A95**
