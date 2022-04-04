# LAB - CI med GitHub actions 

I denne øvingen skal vi se på viktige prinsipper som 

- GitHib actions
- Trunk based development 
- Feature branch
- Branch protection 
- Pull request

Dere blir også kjent med Cloud 9 utviklingsmiljøet dere skal bruke videre. 

## Før dere starter

- Dere trenger en GitHub Konto
- Lag en fork av dette repositoriet inn i deres egen GitHub konto

### Sjekk ut AWS Cloud 9 miljøet ditt

* Logg på Cloud 9 med en URL som typisk ser slik ut (URL er gitt av instruktør)
* Brukernavn er student-navnet ditt feks "glenn.bech", passordet gis av instruktør.
* Hvis du velger "AWS" ikonet på venstremenyen vil du se "AWS Explorer". Naviger gjerne litt rundt I 
* AWS Miljøet ofr å bli kjent.

![Alt text](img/cloud9.png  "a title")

### Lage en klone av din Fork av dette repoet i Cloud 9

* I Cloud 9 terminalen. Kjør 

```shell
git  clone https://github.com/<din bruker>>/<repoo-navn>>.git
```

* Forsøk å kjøre applikasjonen 
```shell
mvn spring-boot:run
```

Du kan teste applikasjonen med CURL fra Cloid 9
```
curl -X POST \
http://localhost:8080/account/1/transfer/2 \
-H 'Content-Type: application/json' \
-H 'Postman-Token: e674b4f3-6e48-41a0-9e6f-de155a4baf02' \
-H 'cache-control: no-cache' \
-d '{
"amount": 1500
}'
```

Husk at dette er applikasjonen "Shakybank", en 500 Internal server error er svært vanlig :-)

## Lag en GitHub Actions workflow 

Bruk editoren i Cloud 9 til å gjøre lage en fil som heter ````.github/workflows/main.yml````

```yaml
# This workflow will build a Java project with Maven, and cache/restore any dependencies to improve the workflow execution time
# For more information see: https://help.github.com/actions/language-and-framework-guides/building-and-testing-java-with-maven
name: Java CI with Maven
on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Set up JDK 11
      uses: actions/setup-java@v2
      with:
        java-version: '11'
        distribution: 'adopt'
        cache: maven
    - name: Build with Maven
      run: mvn -B package --file pom.xml
```

Commit og push koden til ditt repo. 

Dette er en vekdig enkel *workflow* med en *job* som har en rekke *steps*. Koden sjekkes ut. JDK11 konfigureres,
Maven lager en installasjonspakke. 

## Sjekk at workflow er aktiv 

* Gå til din fork av dette repoet på Github 
* Velg "Actions"
* Bekreft at du vil kjøre Actions i ditt repo

Gjør en endring på koden, gjerne i main branch, commit og push. Se at WorkFlowen kjører.

## Konfigurer branch protection på repoet

Vi skal nå sørge for at vi ikke får kode som ikke kompilerer, eller kode med brukkede tester in ni main branch.
Det er også bra skikk å ikke comitte kode direkte på main. 

- Gåt til gitHub.com og din fork av dette repoet.  
- Gå til Settings/Branches og Se etter seksjonen "Branch Protection Rules".
- Velg *Add*
- Velg *main* Som branch
- Velg ````Require status check before passing````

## Brekk en enhetstest 

- Lag en ny branch 
- Endre testen så den feiler
- Comitt og push endringen til GitHub 
- Gå til ditt repo på GitHub.com og forsøk å lage en Pull request fra din branch til main. (OBS! GitHub velger default forken sin kilde når du lager en pull request. Du må endre nedtrekksmenyen til ditt eget repo.)
- Sjekk at du ikke får lov til å merge PR.

## Peer review

- Gåt til gitHub.com og din fork av dette repoet.
- Gå til Settings/Branches og Se etter seksjonen "Branch Protection Rules".
- Velg *Add*
- Velg *main* Som branch
- Velg ````Require approvals before merge````

## Test
 
- Legg til en annen person som collaborator på ditt repo
- Gå til Github og lag en ny Pull request
- Få personen til å godkjenne din pull request
- Forsøk gjerne å fremprovosere en feil ved å få en unit test til å feile. Legg merke til at det fortsatt er mulig å merge til master.
