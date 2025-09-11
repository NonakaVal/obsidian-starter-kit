---
banner: "https://w.wallhaven.cc/full/7j/wallhaven-7j3lve.png"
cssclasses:
banner_y: 0.60476
---
```widgets
type: clock
```
# Começar...

`BUTTON[new]`      `BUTTON[lembrete]` [^1]




---

> [!globe]+ **[[Atlas]]** » [[como + funciona|+]] | [[MOC definição|Mapas]] | [[Coleções]] 
>  >  `BUTTON[dash, nav]`  `BUTTON[col]` `BUTTON[collection]`  [^2]



> [!calendar]+ **[[Calendar]]** » [[DIAS|Dias]] | [[Como Calendário funciona|Reviews]] 
> `BUTTON[today]`   `BUTTON[task]`    [^3]

> [!mountain]+ **[[Efforts]]** » [[Como Esforços funciona|Works]] 
> `BUTTON[areas]`  `BUTTON[project]`  `BUTTON[eff]` [^3]
<br><br>
# Revisar
---

````tabs

tab: ⏱️ Alteradas Recentemente

```dataviewjs
//-----------------------------------------------------
// CONFIGURAÇÃO
//-----------------------------------------------------
const ICONES = {
    "AULAS": "📚",
    "PROJETOS": "🛠️",
    "ESFORÇOS": "⚡",
    "NOTAS": "📝",
    "ATLAS": "🌍",
    "DEFAULT": "📄"
};

const PASTAS_INCLUIR = [
    /.*\+.*/,
    /^ESFORÇOS/,
    /^ATLAS/
];

const PASTAS_EXCLUIR = [
    /^\//
];

//-----------------------------------------------------
// FUNÇÕES AUXILIARES
//-----------------------------------------------------
function deveIncluir(pasta) {
    return PASTAS_INCLUIR.some(regex => regex.test(pasta)) &&
           !PASTAS_EXCLUIR.some(regex => regex.test(pasta));
}

function getIcon(folder) {
    const upperFolder = folder.toUpperCase();
    return ICONES[Object.keys(ICONES).find(key => upperFolder.includes(key))] || ICONES.DEFAULT;
}

function formatarIdade(data) {
    const diff = Date.now() - data.toJSDate().getTime();
    const minutos = diff / 1000 / 60;

    if (minutos < 60) return `${Math.floor(minutos)} min`;
    if (minutos < 1440) return `${Math.floor(minutos / 60)} h`;
    if (minutos < 43200) return `${Math.floor(minutos / 1440)} d`;
    if (minutos < 525600) return `${Math.floor(minutos / 43200)} m`;
    return `${Math.floor(minutos / 525600)} a`;
}

function estilizarLink(p) {
    return `**${dv.fileLink(p.file.path, false, p.file.name)}**`;
}

//-----------------------------------------------------
// COLETA E AGRUPAMENTO
//-----------------------------------------------------
const pages = dv.pages("")
    .where(p => deveIncluir(p.file.folder))
    .sort(p => p.file.mtime, 'desc')
    .limit(20);  // 🔥 Alterado para 10 notas

//-----------------------------------------------------
// EXIBIÇÃO
//-----------------------------------------------------
dv.table(
    ["", "📄 Nota", "🕒 Modificação", "⏳ Desde modificação"],
    pages.map(p => [
        getIcon(p.file.folder),
        estilizarLink(p),
        `\`${p.file.mtime.toFormat("yyyy-MM-dd HH:mm")}\``,
        `\`${formatarIdade(p.file.mtime)}\``
    ])
);

```

---
tab: 📍 Últimas notas criadas
```dataviewjs
//-----------------------------------------------------
// CONFIGURAÇÃO
//-----------------------------------------------------
const ICONES = {
    "AULAS": "📚",
    "PROJETOS": "🛠️",
    "EFFORTS": "⚡",
    "NOTAS": "📝",
    "ATLAS": "🌍",
    "DEFAULT": "📄"
};

// Inclui EFFORTS, ATLAS e qualquer pasta com +
const PASTAS_INCLUIR = [
    /.*\+.*/,       // Pastas com "+"
    /^EFFORTS/,     // Pasta EFFORTS
    /^ATLAS/        // Pasta ATLAS
];

// Exclui apenas pastas de sistema
const PASTAS_EXCLUIR = [
    /^SYSTEM\//     // Ignora pastas de sistema
];

//-----------------------------------------------------
// FUNÇÕES AUXILIARES
//-----------------------------------------------------
function deveIncluir(pasta) {
    return PASTAS_INCLUIR.some(regex => regex.test(pasta)) &&
           !PASTAS_EXCLUIR.some(regex => regex.test(pasta));
}

function getIcon(folder) {
    const upperFolder = folder.toUpperCase();
    for (let key in ICONES) {
        if (upperFolder.includes(key)) return ICONES[key];
    }
    return ICONES.DEFAULT;
}

function formatarIdade(data) {
    const diff = Date.now() - data.toJSDate().getTime();
    const minutos = diff / 1000 / 60;
    
    if (minutos < 60) return `${Math.floor(minutos)} min`;
    if (minutos < 60 * 24) return `${Math.floor(minutos / 60)} h`;
    if (minutos < 60 * 24 * 30) return `${Math.floor(minutos / 60 / 24)} d`;
    if (minutos < 60 * 24 * 365) return `${Math.floor(minutos / 60 / 24 / 30)} m`;
    return `${Math.floor(minutos / 60 / 24 / 365)} a`;
}

//-----------------------------------------------------
// COLETA E AGRUPAMENTO
//-----------------------------------------------------
const pages = dv.pages("")
    .where(p => deveIncluir(p.file.folder))
    .sort(p => p.file.mtime, 'desc')
    .limit(50); // Mais notas, pois agora será por pasta

// Agrupando por pasta principal
const grupos = pages.groupBy(p => p.file.folder.split("/")[0]);

//-----------------------------------------------------
// EXIBIÇÃO POR PASTA
//-----------------------------------------------------
for (let grupo of grupos) {
    dv.header(3, `📂 ${grupo.key} (${grupo.rows.length})`);
    
    dv.table(
        ["Ícone", "Nota", "Modificada em", "Tempo desde modificação"],
        grupo.rows.map(p => [
            getIcon(p.file.folder),
            dv.fileLink(p.file.path, false, p.file.name),
            p.file.mtime.toFormat("yyyy-MM-dd HH:mm"),
            formatarIdade(p.file.mtime)
        ])
    );
}

tab: 

```dataview
TABLE length(file.inlinks) as Total, file.inlinks as Backlinks  
FROM "SISTEMA/COLEÇÕES"
SORT length(file.inlinks) desc


LIMIT 30
```



````

```meta-bind-button
label: Coleções 
hidden: true
icon: layout
class: ""
id: col
style: primary
actions:
  - type: command
    command: obsidian-hotkeys-for-specific-files:SISTEMA/SOBRE/_COLEÇÕES.md
```


```meta-bind-button
label: Criar Coleção
hidden: true
icon: folder
class: ""
id: collection
style: destructive
actions:
  - type: command
    command: quickadd:choice:d223214e-cf0c-4a6a-9d27-bfe62d8542aa
```


````tabs
tab: 🗂️ Totais Coleções

```dataview
TABLE without id file.link as Coleção, length(file.inlinks) as Notas 
FROM "SISTEMA/COLEÇÕES"
SORT length(file.inlinks) desc


LIMIT 30
```

tab: 📂 Coleções  (Links)


```dataview
TABLE length(file.inlinks) as Total, file.inlinks as Backlinks  
FROM "SISTEMA/COLEÇÕES"
SORT length(file.inlinks) desc


LIMIT 30
```

````

```meta-bind-button
label: Nota
hidden: true
icon: plus
class: ""
id: new
style: primary
actions:
  - type: command
    command: quickadd:choice:9dd5d65e-dae6-4ada-8590-069c6fedb6c2
```
```meta-bind-button
label: Navegador de Notas
hidden: true
icon: map
class: ""
id: nav
style: primary
actions:
  - type: command
    command: dashboard-navigator:navigator
```


# Sistema

- [[TEMPLATES]]
- [[Atalhos]]
- [[Mapa Boas Vindas ]]

```meta-bind-button
label: Tarefas
hidden: true
icon: workflow
class: ""
id: task
style: primary
actions:
  - type: command
    command: obsidian-hotkeys-for-specific-files:SISTEMA/TEMPLATES/SNIPPET/% TODAS TAREFAS.md
```




<br><br>
<br><br>


```meta-bind-button
label: Area / Projeto
hidden: true
icon: plus
class: ""
id: eff
style: primary
actions:
  - type: command
    command: quickadd:choice:ebc3941c-ed51-4da4-abb4-bf15c72eb683
```


```meta-bind-button
label: Projetos
hidden: true
icon: swords
class: ""
id: project
style: destructive
actions:
  - type: command
    command: obsidian-hotkeys-for-specific-files:ESFORÇOS/2_PROJETOS.md
```




```meta-bind-button
label: Dias
hidden: true
icon: sun
class: ""
id: dias
style: destructive
actions:
  - type: command
    command: obsidian-hotkeys-for-specific-files:CALENDÁRIO/REVISÕES/DIAS.md
```



```meta-bind-button
label: Areas
hidden: true
icon: sword
class: ""
id: areas
style: destructive
actions:
  - type: command
    command: obsidian-hotkeys-for-specific-files:ESFORÇOS/1_AREAS.md
```



```meta-bind-button
label: Lembrete
hidden: true
icon: plus
class: ""
id: lembrete
style: destructive
actions:
  - type: command
    command: quickadd:choice:7ec7e9a0-be26-4424-9caf-5751f9865da3
```
```meta-bind-button
label: Dashboard
hidden: true
icon: book 
class: ""
id: dash
style: primary
actions:
  - type: command
    command: dashboard-navigator:dashboard
```


```meta-bind-button
label: Nota Diária
hidden: true
icon: calendar
class: ""
id: today
style: destructive
actions:
  - type: command
    command: daily-notes
```




[^1]: Seu ponto de partida e base principal.
	
	- Eu quero... [[Add|Adicionar]] ideias.
	- Eu quero... [[Relate|Relacionar]] ideias.
	- Eu quero... [[Communicate|Comunicar]] ideias.

[^2]: Use as seguintes coleções para navegar rapidamente pelo seu ideaverso:
	
	- Eu quero... navegar pelo meu conhecimento 
		- [[Maps|Mapas]], [[Views|Visualizações]], [[Collections|Coleções]]
	- Eu quero... navegar pelas minhas fontes 
		- [[Sources|Fontes]], [[Books|Livros]], [[Movies|Filmes]], [[Series|Séries]]
	- Eu quero... navegar pelos meus esforços 
		- [[Efforts|Esforços]]
	- Eu quero... navegar pelo meu mundo 
		- [[People|Pessoas]], [[Entities|Entidades]], [[Meetings|Reuniões]]
	- Eu quero... navegar por ideias 
		- [[Things|Coisas]], [[Statements|Declarações]], [[Concepts|Conceitos]], [[Quotes|Citações]], [[Questions|Perguntas]]
	
	

[^3]: Use seu ideaverso para assumir o controle da sua vida e ser mais intencional sobre as coisas:
	
	- Eu quero... refletir sobre minha vida 
		- [[Life Map|Mapa da Vida]]
	- Eu quero... fazer registros sobre coisas específicas 
		- [[Logs|Registros]]
	- Eu quero... ser mais intencional sobre minha vida 
		- [[Plan and Review|Planejar e Revisar]]
	
