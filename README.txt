LitAfrika — version.json & _headers (guide rapide)

Ce pack contient :
- version.json : métadonnées de la dernière version de l’APK
- _headers     : règles Netlify pour éviter le cache
- Ce fichier README : comment mettre à jour et à quoi ça sert

1) À quoi servent ces fichiers ?
--------------------------------
• version.json
  - Fichier lu par le site et/ou l’app pour connaître la version la plus récente.
  - Évite de changer le HTML à chaque release : on met juste à jour ce JSON.
  - Peut afficher la version sur la page et générer le QR/lien automatiquement si besoin.

• _headers (Netlify)
  - Désactive le cache navigateur/CDN pour version.json et les APK du dossier downloads/.
  - Garantit que les visiteurs récupèrent toujours la dernière version, pas une copie mise en cache.

2) Où les placer ?
------------------
- Mettez les 2 fichiers à la racine du site (même dossier que index.html).
- Le dossier downloads/ doit exister (avec votre APK dedans).
- Votre index.html doit pointer vers :
    downloads/litafrika-latest-site.apk
  …ou vers l’URL que vous indiquerez dans version.json (champ "apk_url").

3) Comment mettre à jour à chaque nouvelle version ?
----------------------------------------------------
Étapes classiques :
a. Construisez la nouvelle APK.
b. Remplacez le fichier stable :
     downloads/litafrika-latest-site.apk
   (Optionnel : gardez une archive versionnée en plus, ex. downloads/litafrika-1.0.1-site.apk)

c. Calculez le SHA-256 :
   - Windows (PowerShell) :
       Get-FileHash .\downloads\litafrika-latest-site.apk -Algorithm SHA256
   - macOS / Linux :
       shasum -a 256 downloads/litafrika-latest-site.apk

d. Ouvrez version.json et mettez à jour les champs :
   - "version" : ex. "1.0.1"
   - "date"    : ex. "2025-10-02" (AAAA-MM-JJ)
   - "size"    : ex. "≈ 25 MB" (optionnel)
   - "sha256"  : collez la valeur calculée
   - "apk_url" : laissez "downloads/litafrika-latest-site.apk" (nom stable)
                 ou remplacez par une URL versionnée si vous préférez (ex. "downloads/litafrika-1.0.1-site.apk")
   - "notes"   : court texte de release (optionnel)

e. Déployez le site (Netlify). _headers évite l’ancien cache.

4) Dois-je modifier index.html à chaque fois ?
----------------------------------------------
• Si vous gardez le NOM STABLE "litafrika-latest-site.apk" :
  - Non. Vous ne touchez pas index.html.
  - Vous remplacez l’APK et mettez à jour version.json, puis déployez.

• Si vous changez "apk_url" pour un NOM VERSIONNÉ :
  - OUI, seulement si votre HTML ne lit pas version.json.
  - Dans nos versions récentes d’index.html, le bouton peut être mis à jour automatiquement par JS
    si vous décidez d’utiliser "apk_url" depuis version.json. Si vous utilisez ce mode,
    vérifiez que le script lit bien version.json et applique meta.apk_url → href du bouton et QR.

5) Problèmes de cache / forcer le rafraîchissement
--------------------------------------------------
- _headers désactive le cache sur /version.json et /downloads/*.
- En cas de doute côté JS, vous pouvez ajouter un cache-buster lors des fetch de version.json :
    fetch('version.json?t=' + Date.now(), { cache: 'no-store' })
- Si les utilisateurs voient encore l’ancienne APK, demandez-leur d’actualiser (Ctrl/Cmd + Shift + R).

6) Bonnes pratiques
-------------------
- Gardez un changelog court dans "notes".
- Conservez une archive des anciennes APKs (facultatif mais utile).
- Vérifiez toujours la valeur SHA-256 affichée sur le site après déploiement.
- Testez le QR code avec un téléphone avant d’annoncer la release.

7) Exemple de version.json
--------------------------
{
  "version": "1.0.1",
  "date": "2025-10-02",
  "size": "≈ 25 MB",
  "sha256": "VOTRE_SHA256_ICI",
  "apk_url": "downloads/litafrika-latest-site.apk",
  "notes": "Corrections de bugs et améliorations des performances."
}

8) Foire aux questions
----------------------
• Est-ce que _headers marche avec Vercel / Cloudflare Pages ?
  - _headers est propre à Netlify. Sur Vercel, utilisez vercel.json (headers).
    Sur Cloudflare Pages, configurez les Cache-Control via Workers/Rules.
• Puis-je afficher la version sur la page ?
  - Oui : soit vous l’écrivez à la main dans index.html, soit vous faites un fetch de version.json et
    mettez à jour un <span id="apkVersion"> dans le DOM.
• Est-ce que je peux laisser "apk_url" sur un fichier versionné ?
  - Oui. Avantage : vous gardez l’historique. Inconvénient : si votre index.html ne lit pas version.json,
    vous devrez changer le href manuellement.

Bon déploiement !
