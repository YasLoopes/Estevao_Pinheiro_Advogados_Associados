---
name: mobile-ep
description: >
  Refatora páginas HTML do projeto E&P para mobile-first. Aplica clamp(), media queries e overflow guards seguindo o padrão visual do escritório (bordeaux/gold, DM Sans/Serif/Mono). Use quando uma nova página HTML for criada e precisar de ajuste responsivo.
---

# /mobile-ep — Refatoração mobile-first

Aplica o padrão responsivo dos documentos do Estevão & Pinheiro em qualquer página HTML nova.

---

## Checklist de execução

### 1. Body base
```css
body { font-size: 1rem; line-height: 1.6; overflow-x: hidden }
```

### 2. Tipografia com clamp()
Títulos principais:
```css
font-size: clamp(1.25rem, 4vw, 2rem)
```
Subtítulos:
```css
font-size: clamp(1rem, 3vw, 1.5rem)
```
Corpo mínimo no mobile: `1rem` (nunca abaixo de 0.875rem para texto de leitura).

### 3. Padding e margin com clamp()
```css
padding: clamp(1rem, 4vw, 2.5rem) clamp(1rem, 4vw, 1.5rem)
```

### 4. Grids — colapso por breakpoint
| Grid original | Mobile | Tablet (540px+) | Desktop (768px+) |
|---|---|---|---|
| `repeat(3,1fr)` | `1fr` | `1fr 1fr` | `repeat(3,1fr)` |
| `repeat(4,1fr)` | `1fr 1fr` | `repeat(4,1fr)` | — |
| `1fr 1fr` | `1fr` | `1fr 1fr` | — |
| `auto 1fr` (avatar+texto) | `1fr` | `auto 1fr` | — |

```css
.grid-3 { grid-template-columns: 1fr }
@media(min-width:480px){ .grid-3 { grid-template-columns: 1fr 1fr } }
@media(min-width:768px){ .grid-3 { grid-template-columns: repeat(3,1fr) } }
```

### 5. Tabelas — scroll horizontal
```css
.table-wrap { overflow-x: auto; -webkit-overflow-scrolling: touch; border-radius: 8px }
```
Em CSS puro (sem wrapper HTML):
```css
@media(max-width:640px){
  .minha-tabela { display: block; overflow-x: auto; -webkit-overflow-scrolling: touch }
}
```

### 6. Nav — esconder links em mobile
```css
@media(max-width:640px){
  .nav-links { display: none }
  .top-nav { padding: 10px 16px }
}
```

### 7. Flex — garantir quebra de linha
```css
flex-wrap: wrap
```

### 8. Print — sempre preservar
```css
@media print {
  body { background: #fff }
  .table-wrap { overflow: visible }
}
```

---

## Breakpoints padrão do projeto E&P

| Nome | Largura | Uso |
|---|---|---|
| Mobile pequeno | ≤420px | Ajustes finos de KPI/valor |
| Mobile portrait | ≤640px | Layout principal mobile |
| Tablet | ≤768px | Grids de 2 colunas |
| Desktop | ≥1024px | Layout completo |

---

## Padrão de execução

1. Ler o bloco `<style>` do arquivo
2. Identificar: grids com colunas fixas, font-sizes em px, paddings fixos, tabelas sem scroll
3. Corrigir `body { font-size }` para `1rem`
4. Substituir o CSS ou adicionar bloco `/* RESPONSIVE */` ao final
5. Commitar com mensagem `[arquivo]: mobile-first refactor`
