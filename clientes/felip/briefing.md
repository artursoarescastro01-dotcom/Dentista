# Briefing — felip

- **Data:** 2026-08-12
- **Cliente/projeto:** felip
- **Tipo:** Cliente
- **Objetivo principal:** Site de portfólio/apresentação pessoal
- **Entregas previstas:** Site

## Identidade visual

- **Nome/marca:** Felipe Salton — @saltondesign (Brand Designer, também atua com social media)
- **Paleta:**
  - Preto `#000000` (fundo)
  - Branco `#F9F9F9` (texto)
  - Cinzas de apoio: `#646464`, `#A7A7A7`, `#333333`
  - Degradê laranja em destaques: `#E85002` → `#C10801` → `#F16001`
- **Estilo:** site preto, textos brancos, degradê laranja em destaques/textos-chave
- **Fontes:** Melodrame (headlines), Aeonik Pro (texto de apoio) — arquivos ainda não recebidos, ver `site/assets/fonts/README.md`
- **Referência de estilo:** eichstudio.com (clima/estrutura, não copiar)
- **Tom:** moderno e profissional

## Detalhes a coletar

- [x] Nome completo / marca a exibir
- [ ] Áreas de atuação / serviços — confirmar se é só "Identidade de Marca" + "Social Media" ou tem mais
- [ ] Seções desejadas (sobre, projetos, contato, etc.) — v1 já criada com sobre/serviços/trabalhos/contato, validar com Felipe
- [ ] Textos e imagens a usar — v1 está com placeholders
- [x] Referências visuais / sites de inspiração
- [x] Onde vai hospedar (por enquanto): Vercel, projeto `felip-site` — https://felip-site.vercel.app — domínio próprio ainda não definido
- [x] Cases reais na seção "Trabalhos" (Studio Caiaque, Café Momentos) — com imagem de capa real, cada card linkando pro case completo no Behance
- [ ] Foto do Felipe pra seção "Sobre"
- [ ] Arquivos das fontes (Melodrame e Aeonik Pro em .woff2)
- [ ] Número de WhatsApp e e-mail de contato reais

## Status

v1 da estrutura do site criada em `site/` (HTML/CSS/JS puro, sem
framework) — hero, sobre, serviços, trabalhos e contato, com a
paleta e o clima acima. Textos e imagens ainda são placeholder.

Seção "Trabalhos" já tem os 2 primeiros cases reais (Studio Caiaque,
Café Momentos) com imagem de capa e link pro case completo no
Behance — Felipe mandou os links e as imagens em 2026-08-13.
Imagens em `site/assets/img/studio-caiaque-capa.jpg` e
`site/assets/img/cafe-momentos-capa.jpg`.

Site publicado (deploy de produção) na Vercel em 2026-08-13.
Testamos deploy anônimo na Netlify antes, mas ficou preso atrás de
senha de acesso mesmo depois do claim; a Vercel serviu o site público
direto, sem gate.

**Atenção — projeto Vercel correto:** o primeiro deploy linkou sem
querer com um projeto chamado `site` (genérico, 12 dias mais velho,
sem relação com o Felipe — provável sobra de teste antiga, não
tocamos nele). Isso aconteceu porque a pasta local se chama `site` e
a CLI da Vercel linka automaticamente por nome. Corrigido: a pasta
`site/` está linkada (`.vercel/project.json`) ao projeto certo,
**`felip-site`**, link final:

**https://felip-site.vercel.app**

Pra atualizar depois de mudanças, rodar `npx vercel deploy --yes --prod`
dentro de `site/` — vai pro projeto certo automaticamente agora que
está linkado. Não renomear/mover a pasta `site/` sem re-linkar, e não
rodar deploy de dentro de outra pasta de cliente usando esse mesmo
`.vercel` (cada cliente deve ter seu próprio link de projeto).

Corrigido em seguida: o fundo 3D do hero dependia do Three.js via CDN
externo (cdn.jsdelivr.net), o que falha em silêncio com bloqueador de
anúncios/rede restrita (deixa o hero preto, sem a forma 3D). Agora a
lib está em `site/assets/vendor/three.module.js`, servida pelo próprio
site — sem dependência externa.

Mesmo com isso, o hero continuou aparecendo sem a forma 3D no Edge
"normal" (não headless) do Artur, mesmo confirmando que não havia
bloqueador ativo — ou seja, provavelmente é aceleração de gráficos
desligada/driver de vídeo específico daquela máquina (não deu pra
confirmar 100%, mas testes em Edge headless e no preview automático
da Vercel sempre mostravam certo). WebGL depende de GPU/driver/
configuração de cada computador e não dá pra garantir que funciona
"em qualquer navegador" — não é confiável o bastante pra mostrar
pro cliente.

**Decisão (2026-08-13):** o Felipe preferiu manter o efeito 3D mesmo
sabendo do risco de não renderizar em todo navegador/computador.
Config final do hero, em camadas:
1. Fundo CSS puro sempre visível (`hero-glow-drift`, radial-gradients
   na paleta da marca, respeita `prefers-reduced-motion`) — nunca
   fica vazio.
2. Por cima, `js/hero-3d.js` tenta renderizar o torus 3D
   (Three.js/WebGL, lib local em `assets/vendor/three.module.js`,
   sem CDN externo). Só aparece (`.hero-canvas.is-ready`) depois do
   primeiro frame renderizado com sucesso; se falhar, fica invisível
   e só o gradiente CSS aparece.

Se no futuro quiser abrir mão do WebGL de vez, considerar Canvas 2D
(sem depender de GPU) — já existiu uma versão assim no histórico do
git antes de trocar pro Three.js.
