
# ft_transcendence – Résolution du problème BabylonJS (canvas noir)

## 📌 Contexte du bug
Dans la page `/pong`, le canvas restait noir et la console affichait :
```
Uncaught ReferenceError: BABYLON is not defined
```

## 🔍 Cause principale
- On essayait de faire un import direct depuis un CDN (ex. : `import * as BABYLON from 'https://cdn.babylonjs.com/babylon.js'`).
- TypeScript ne sait pas importer directement des fichiers CDN en tant que modules ES.
- Résultat : la variable `BABYLON` n’était **jamais définie** au moment où on voulait l’utiliser.

## ✅ Solution mise en place
### 1️⃣ Utiliser la version **globale** de BabylonJS via des `<script>`
Dans le template HTML de la page `pong.ts`, on a ajouté :
```html
<script src="https://cdn.babylonjs.com/babylon.js"></script>
<script src="https://cdn.babylonjs.com/materialsLibrary/babylonjs.materials.min.js"></script>
```

Ces balises chargent BabylonJS dans le **contexte global** du navigateur.

---

### 2️⃣ Déclarer `BABYLON` comme une variable globale dans le TypeScript
Dans `pong.ts`, au lieu de faire un `import`, on a ajouté :
```ts
declare const BABYLON: any;
```

Ça indique à TypeScript :  
> “Ne t’inquiète pas, `BABYLON` existe déjà quelque part dans la page (grâce aux `<script>`).”

---

### 3️⃣ Attendre le chargement complet de la page
On a encapsulé le code BabylonJS dans :
```ts
window.addEventListener("load", () => {
  // Tout le code BabylonJS ici (création de la scène, etc.)
});
```

Cela garantit que :
✅ Le HTML est prêt  
✅ Les `<script>` ont bien été chargés avant d’utiliser `BABYLON`

---

## 🎯 Résultat final
- Le canvas 3D s’affiche correctement avec la sphère, le sol, et la lumière.
- Plus d’erreurs `BABYLON is not defined`.
- Le code TypeScript reste propre et compile sans erreur.

---

## 🧩 Résumé final
✔️ **Problème** : TypeScript n’accepte pas les imports CDN.  
✔️ **Solution** : Charger les scripts via `<script>` dans le HTML + `declare const BABYLON` pour TypeScript.  
✔️ **Pourquoi ça marche** : Les scripts `<script>` sont globaux (comme jQuery ou Three.js quand ils sont dans un `<script>`). TypeScript comprend ça avec `declare const`.

---

