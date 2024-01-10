## Chapitre 1

J'utilise Infomaniak comme fournisseur de nom de domaine, j'accède à mon Dashboard sur leur site puis je me rends dans le menu dédié au nom de domaine que j'utilise pour ce brief.

Je clique sur "Modifier la zone DNS" afin de pouvoir modifier le comportement de mon nom de domaine.

![1](https://hackmd.io/_uploads/HJHgC4CHa.png)

J'ajoute une nouvelle règle A.

![2](https://hackmd.io/_uploads/HyaZRVRSp.png)
![3](https://hackmd.io/_uploads/S16WA40Ha.png)

J'écris l'IP publique actuelle de mon application (que je remplacerai  plus tard par l'adresse IP publique mon Application Gateway).

![4](https://hackmd.io/_uploads/BJMI0NCSp.png)

## Chapitre 2

Je crée un `venv` avec la commande : `python3 -m venv venv` afin de pouvoir installer la librairie nécessaire dans un environnement séparé de mon environnement habituel.

J'y accède via la commande : `source venv/bin/activate`.

Puis j'installe avec `pip install certbot-dns-infomaniak` [l'outil fourni certbot](https://github.com/Infomaniak/certbot-dns-infomaniak) fourni par infomaniak.

![1](https://hackmd.io/_uploads/Byn76QRSa.png)

Depuis mon dashboard infomaniak je crée un nouveau token qui  permettra à l'outil que je viens d'installer d'effectuer des actions en ma place.

![2](https://hackmd.io/_uploads/ByGOamCS6.png)

Puis j'éxécute cette commande qui générera un certificat lié à mon nom de domaine.

```sh
export INFOMANIAK_API_TOKEN=MON_TOKEN

sudo --preserve-env=INFOMANIAK_API_TOKEN certbot certonly \
  --authenticator dns-infomaniak \
  --server https://acme-v02.api.letsencrypt.org/directory \
  --agree-tos \
  --rsa-key-size 4096 \
  -d 'domaine.fr'
```

![3](https://hackmd.io/_uploads/HkLTpXRr6.png)

## Chapitre 3
Je commence par récupérer et convertir ce certificat au format pkcs12 avec la commande : `openssl pkcs12 -export -out certificate.pfx -inkey privkey.pem -in cert.pem`, et j'ajoute un mot de passe à mon certificat.

![1](https://hackmd.io/_uploads/rJLfAmCST.png)

Puis je crée mon Azure Application Gateway :

![2](https://hackmd.io/_uploads/HJzohNRHp.png)

Selon la documentation je dois également créer un subnet séparé pour ma machine virtuelle.

![3](https://hackmd.io/_uploads/Hk4pAmAra.png)

![4](https://hackmd.io/_uploads/BkfkJ40rT.png)

J'active le HTTPS sur le port 443 de mon Application Gateway :

![2.1](https://hackmd.io/_uploads/rk2XpVRB6.png)

Je termine par créer ma machine virtuelle qui contiendra mon application.

![5](https://hackmd.io/_uploads/S1YykECBT.png)

## Chapitre 4

Je crée un Azure KeyVault avec l'az cli, dans mon `rg-brief-5` : 

![1](https://hackmd.io/_uploads/SJw91VCSa.png)

J'importe mon certificat (celui au format pkcs12) :

![2](https://hackmd.io/_uploads/H1b7gVAHa.png)

Puis je verifie qu'il a correctement été importé avec la commande : 
`az keyvault certificate show -v`.

![3](https://hackmd.io/_uploads/SkOv5EAST.png)
