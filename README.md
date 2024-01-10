# Brief 5 : DNS, TLS et plus si affinité
## Chapitre 1

J'utilise Infomaniak comme fournisseur de nom de domaine, j'accède à mon Dashboard sur leur site puis je me rends dans le menu dédié au nom de domaine que j'utilise pour ce brief.

Je clique sur "Modifier la zone DNS" afin de pouvoir modifier le comportement de mon nom de domaine.

![1](https://github.com/DevSoleo/simplon-brief-5/blob/main/images/image_1.png)

J'ajoute une nouvelle règle A.

![2](https://github.com/DevSoleo/simplon-brief-5/blob/main/images/image_2.png)
![3](https://github.com/DevSoleo/simplon-brief-5/blob/main/images/image_3.png)

J'écris l'IP publique actuelle de mon application (que je remplacerai  plus tard par l'adresse IP publique mon Application Gateway).

![4](https://github.com/DevSoleo/simplon-brief-5/blob/main/images/image_4.png)

## Chapitre 2

Je crée un `venv` avec la commande : `python3 -m venv venv` afin de pouvoir installer la librairie nécessaire dans un environnement séparé de mon environnement habituel.

J'y accède via la commande : `source venv/bin/activate`.

Puis j'installe avec `pip install certbot-dns-infomaniak` [l'outil fourni certbot](https://github.com/Infomaniak/certbot-dns-infomaniak) fourni par infomaniak.

![5](https://github.com/DevSoleo/simplon-brief-5/blob/main/images/image_5.png)

Depuis mon dashboard infomaniak je crée un nouveau token qui  permettra à l'outil que je viens d'installer d'effectuer des actions en ma place.

![6](https://github.com/DevSoleo/simplon-brief-5/blob/main/images/image_6.png)

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

![7](https://github.com/DevSoleo/simplon-brief-5/blob/main/images/image_7.png)

## Chapitre 3
Je commence par récupérer et convertir ce certificat au format pkcs12 avec la commande : `openssl pkcs12 -export -out certificate.pfx -inkey privkey.pem -in cert.pem`, et j'ajoute un mot de passe à mon certificat.

![8](https://github.com/DevSoleo/simplon-brief-5/blob/main/images/image_8.png)

Puis je crée mon Azure Application Gateway :

![9](https://github.com/DevSoleo/simplon-brief-5/blob/main/images/image_9.png)

Selon la documentation je dois également créer un subnet séparé pour ma machine virtuelle.

![10](https://github.com/DevSoleo/simplon-brief-5/blob/main/images/image_10.png)

![11](https://github.com/DevSoleo/simplon-brief-5/blob/main/images/image_11.png)

J'active le HTTPS sur le port 443 de mon Application Gateway :

![12](https://github.com/DevSoleo/simplon-brief-5/blob/main/images/image_12.png)

Je termine par créer ma machine virtuelle qui contiendra mon application.

![13](https://github.com/DevSoleo/simplon-brief-5/blob/main/images/image_13.png)

## Chapitre 4

Je crée un Azure KeyVault avec l'az cli, dans mon `rg-brief-5` : 

![14](https://github.com/DevSoleo/simplon-brief-5/blob/main/images/image_14.png)

J'importe mon certificat (celui au format pkcs12) :

![15](https://github.com/DevSoleo/simplon-brief-5/blob/main/images/image_15.png)

Puis je verifie qu'il a correctement été importé avec la commande : 
`az keyvault certificate show -v`.

![14](https://github.com/DevSoleo/simplon-brief-5/blob/main/images/image_16.png)
