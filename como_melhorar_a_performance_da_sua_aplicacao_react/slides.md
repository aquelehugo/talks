---
marp: true
size: 4:3
---

# Como melhorar a performance na sua aplicação React

com Hugo Lopes ([aquelehugo.com](https://aquelehugo.com))
& Victor Fernandes ([LinkedIn](https://www.linkedin.com/in/victor-beir%C3%A3o-fernandes-2bb11414a/
))

---

## O que é performance?

---

- Conceitos
	- [Core web vitals](https://web.dev/vitals/)
		- LCP (Largest contentful paint)
		- FID (First input delay)
		- CLS (Content layout shift)
		- Código que é executado a partir de interação do usuário não entra nas métricas
	- Performance percebida
		- lazy loading
		- resposta otimista
	- Uso de recursos (rede, processamento e memória)

---

- Ferramentas
	- https://web.dev/vitals-tools/
		- [Lighthouse](https://developers.google.com/web/tools/lighthouse)
	- React Dev Tools
		- [extensão para Chrome](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi)
		- [extensão para Firefox](https://addons.mozilla.org/en-US/firefox/addon/react-devtools/)
	- Aba "Performance" do devtools nativo do navegador
		- https://developer.mozilla.org/en-US/docs/Tools/Performance
		- https://developer.chrome.com/docs/devtools/overview/#performance

---

## Por que devemos nos preocupar com performance?

---

- SEO
	- conteúdo ainda é mais importante, mas o Google considera os web vitals
- UX
	- sites mais responsivos (resposta da aplicação) convertem mais
		- [Amazon found every 100ms of latency cost them 1% in sales](https://www.gigaspaces.com/blog/amazon-found-every-100ms-of-latency-cost-them-1-in-sales)
	- problemas de performance se multiplicam em condições ruins de rede/processamento

---

## Otimize apesar do React

- Otimizar o carregamento dos recursos (CSS, JS, fontes)
	- [How to avoid layout shifts caused by web fonts](https://simonhearne.com/2021/layout-shifts-webfonts/)
	- fontes podem influenciar muito o LCP
	- verificar o impacto de recursos de terceiros
- Otimizar o acesso ao servidor (CDN, cache)
- Otimizar o conteúdo
	- formatos de imagens para web
	- imagens em tamanhos ideais
	- quantidade de conteúdo inicial (não exibir de início o que não é essencial)

---

## Performance com React

---

### CSR (client side rendering)

---

- prós:
	- TTFB (time to first byte) baixo
	- sem HTML duplicado
	- depois do primeiro acesso, a aplicação toda pode estar no cache
	- mais barato para hospedar
	- implementação mais simples

---

- contras:
	- precisar carregar todo JS e renderizar antes de mostrar qualquer conteúdo
	- conteúdo demora mais para ser crawleado
		- sabe-se lá como funciona além do Google
	- algumas medidas de SEO são impossíveis
		- ex.: og tags que dependem de dados assíncronos

---

### SSR (server side rendering)

---

- prós:
	- conteúdo visível antes do carregamento do JS
	- mais crawleável que CSR
		- og tags podem ser populadas no servidor
		- qualquer crawler vai obter o conteúdo pronto

---

- contras:
	- aumento da complexidade da aplicação
		- tratar ou substituir APIs do navegador no servidor
	- sem cache o tempo de resposta do servidor pode subir consideravelmente
	- maiores custos de implementação
	- download de JS com conteúdo que já foi renderizado
	- processo de hidratação executa o código dos componentes no cliente de novo
		- obrigatório quando a página é interativa

---

### SSG (static site generators)

---

- prós:
	- TTFB (time to first byte) baixo
	- conteúdo visível antes do carregamento do JS
	- mais crawleável que CSR
		- og tags podem ser populadas no servidor
		- qualquer crawler vai obter o conteúdo pronto

---

- contras:
	- aumento da complexidade da aplicação
		- tratar ou substituir APIs do client no servidor
	- quanto mais conteúdo, maior o tempo de deploy
	- não serve para sites que atualizam o conteúdo com frequência

---

### Nota

- Existem abordagens híbridas prontas, como o que faz o NextJS

---

## Você precisa de React?

---

- um site estático pode usar um gerador como [Hugo](https://gohugo.io/) ou [Jekyll](https://jekyllrb.com/)
- um site que apenas renderiza conteúdo de uma API pode usar bibliotecas menores
- se você não está buscando componentização e reatividade, provavelmente não precisa

---

## Onde focar a performance

---

- Componentes muito grandes ou com dependências grandes
	- React pode ser substituído por [Preact](https://preactjs.com/)
	- [Redux pode ser desnecessário](https://redux.js.org/faq/general#when-should-i-use-redux)
- Muito conteúdo / mudanças drásticas no DOM
- Componentes complexos que se repetem ou sempre estão na tela
- Funções custosas (criptografia, tratamento de strings muito grandes, etc)
- Timers (`setInterval` ou `setTimeout` recursivo)
- Eventos que disparam múltiplas vezes (scroll, resize)
- Requisições a APIs

---

## Carregamento assíncrono de componentes e bibliotecas (code splitting)

---

- [React.lazy](https://reactjs.org/docs/code-splitting.html#reactlazy) para CSR
- [loadable-components](https://loadable-components.com/) para SSR
	- [React 18](https://reactjs.org/blog/2021/06/08/the-plan-for-react-18.html#whats-coming-in-react-18) pode tornar desnecessário
- [import()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import#dynamic_imports)
- podem ser carregados a partir de uma interação do usuário
- reduzir o bundle da aplicação também gera ganho no parsing do JS

---

## Como lidar com grandes quantidades de conteúdo

---

- carregar aos poucos
- libs de virtualização podem ajudar
	- https://addyosmani.com/blog/react-window/
	- Exemplo: https://tmdb-viewer.surge.sh/
- processar como string em outra thread com web workers
- processar string com Web Assembly

---

## Otimizando renderização no React

---

### React.memo e useMemo

---

- [Before you memo](https://overreacted.io/before-you-memo/)
- componentes como Header e itens de uma lista dinâmica são bons exemplos para usar React.memo
- cuidado com as propriedades que são funções e objetos, o que importa é a referência
- cuidado com a otimização prematura
	- se o componente não é renderizado várias vezes, não vale a pena
	- se o componente ou a função não são complexos, também não

---

- exemplos de código:
	- https://github.com/aquelehugo/react-performance-techniques/tree/main/use-memo
	- https://github.com/aquelehugo/react-performance-techniques/tree/main/react-memo

---

### Outras APIs

---

- useState
	- pode receber callback para obter estado inicial, o que evita que um estado inicial custoso seja executado a cada render

---

- useCallback vs useRef para funções
	- se a função não tem dependência, pode ficar fora do componente sem usar hooks
	- não é bom usar `useRef` para funções sem dependência porque uma dependência pode ser adicionada posteriormente e o linter não vai avisar
	- quando a função possui dependências, a hook `useCallback` pode ser usada para manter a referência

---

- Contexto
	- ideal é não ter contextos complexos (propriedades que nem sempre são usadas juntas), se possível dividir em mais contextos
	- se for inevitável, a performance pode ser melhorada com o [useContextSelector](https://github.com/dai-shi/use-context-selector) ou biliotecas de gerenciamento de estado global como Redux

---

## Cache de requisições no cliente

---

- Clientes
	- Apollo Client
	- urql
	- React Query
- Melhoria na performance percebida
- Aplicação offline-first

---

## Bônus: deixe a hidratação para depois

---

isso é sobre React, beba água :potable_water:

---

- como renderizamos a aplicação no servidor, não precisamos fazer o hydrate imediatamente
	- hidratação parcial
		- https://github.com/facebook/react/issues/12617
		- https://docs.reactstorefront.io/guides/lazy_hydration
			- [LazyHydrate.js](https://github.com/storefront-foundation/react-storefront/blob/489f632aa588b8286e1c621771c5f11e91b07d7f/src/LazyHydrate.js#L110)
	- hidratação completa a partir de uma interação
		- exemplo: uma aplicação onde as interações só acontecem em forms poderia iniciar a hidratação ao focar um campo

---

- exemplos:
	- https://github.com/aquelehugo/react-performance-techniques/tree/main/lazy-full-hydration

---

## Mais sobre performance na web

- [Techniques for improving site performance](https://web.dev/fast/)
- [Critical rendering path](https://developers.google.com/web/fundamentals/performance/critical-rendering-path)
- [RAIL model](https://web.dev/rail/)
- [PRPL pattern](https://web.dev/apply-instant-loading-with-prpl/)
- [Blog sobre performance do Simon Hearne](https://simonhearne.com/)
- [Canal Chrome Developers](https://www.youtube.com/channel/UCnUYZLuoy1rq1aVMwx4aTzw)

---
