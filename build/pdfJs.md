## pdf.js

Est utilisé pour afficher un apperçu du PDF. L'application peut naviguer dans les pages du PDF en utilisant les API PDF.JS.

1. Ecriture du code : inclure les fichiers de scipt pdf.js

```JS
<script src="js/pdf.js"></script>
<script src="js/pdf.worker.js"></script>
```

Les fichiers PDF.JS sont assez gros. Il vaut mieux que vous les minimisiez. Vous pouvez utiliser un minuteur [Minifier JS](https://javascript-minifier.com/) en ligne .

2. Préparation du code HTML

```HTML
<button id="upload-button">Select PDF</button> 
<input type="file" id="file-to-upload" accept="application/pdf" />

<div id="pdf-main-container">
    <div id="pdf-loader">Loading document ...</div>
    <div id="pdf-contents">
        <div id="pdf-meta">
            <div id="pdf-buttons">
                <button id="pdf-prev">Previous</button>
                <button id="pdf-next">Next</button>
            </div>
            <div id="page-count-container">Page <div id="pdf-current-page"></div> of <div id="pdf-total-pages"></div></div>
        </div>
        <canvas id="pdf-canvas" width="400"></canvas>
        <div id="page-loader">Loading page ...</div>
    </div>
</div>

```

- file-to-upload est le type d'entrée de fichier par lequel l'utilisateur choisit un PDF.
- pdf-loader est le conteneur dans lequel un message "PDF Loading" serait affiché pendant le chargement du PDF.
- pdf-prev & # pdf-next sont des boutons qui iront à la page précédente et suivante du PDF.
- pdf-current-page contiendra la page courante no du PDF.
- pdf-total-pages contiendra le nombre total de pages dans le PDF.
- pdf-canvas est l'élément canvas où le PDF sera rendu.
- page-loader montrera un message de «chargement de page» pendant qu'une page est rendue.

3. Définition de certaines variables Javascript

Nous avons besoin de quelques variables globales qui contiendront les propriétés utilisées dans le code.

```JS
var __PDF_DOC,
    __CURRENT_PAGE,
    __TOTAL_PAGES,
    __PAGE_RENDERING_IN_PROGRESS = 0,
    __CANVAS = $('#pdf-canvas').get(0),
    __CANVAS_CTX = __CANVAS.getContext('2d');
```

- __PDF_DOC conservera l' objet PDFDocumentProxy transmis dans le rappel de la promesse getDocument .
- __CURRENT_PAGE contiendra le numéro de la page en cours. 
- __TOTAL_PAGES contiendra le nombre total de pages dans le PDF.
- __PAGE_RENDERING_IN_PROGRESS est un drapeau qui tiendra si un rendu en cours ou non. Si le rendu est en cours Les boutons Précédent et Suivant seront désactivés afin de ne pas provoquer une discordance de contenu de page . Rappelez-vous que le rendu de page est asynchrone, il faudra au moins quelques millisecondes pour afficher une page.
- __CANVAS et __CANVAS_CTX contiendront respectivement l'élément canvas et son contexte.

4. Manipulation du pdf avec le javascript

- showPDF charge le fichier PDF. Il accepte l'URL du PDF comme paramètre. En cas de chargement réussi, il appelle la fonction showPage qui affiche la première page du PDF.

- showPage charge et restitue une page spécifiée du PDF. Pendant le rendu d'une page, les boutons Précédent et Suivant sont désalignés. Un point très important est de noter que nous devons changer l'échelle de la page rendue selon la largeur de l'élément canvas. Dans ce cas, la largeur de l'élément canvas est inférieure à la largeur réelle du PDF. Lorsque PDF est rendu dans le canevas, il doit être réduit.

Les gestionnaires d'événements sur les boutons Précédent / Suivant décrémentent / incrémentent la page affichée et appellent la fonction showPage .

